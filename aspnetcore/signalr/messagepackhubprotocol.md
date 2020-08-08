---
title: Usare il protocollo dell'hub MessagePack in SignalR per ASP.NET Core
author: bradygaster
description: Aggiungere il protocollo dell'hub MessagePack a ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 8e590c87f75d35cbafde1adbc87dea9c45eac92d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022550"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="a2436-103">Usare il protocollo dell'hub MessagePack in SignalR per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2436-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a2436-104">Questo articolo presuppone che il lettore abbia familiarità con gli argomenti trattati in [Introduzione](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="a2436-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="a2436-105">Che cos'è MessagePack?</span><span class="sxs-lookup"><span data-stu-id="a2436-105">What is MessagePack?</span></span>

<span data-ttu-id="a2436-106">[MessagePack](https://msgpack.org/index.html) è un formato di serializzazione binaria veloce e compatto.</span><span class="sxs-lookup"><span data-stu-id="a2436-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="a2436-107">È utile quando le prestazioni e la larghezza di banda rappresentano un problema perché crea messaggi più piccoli rispetto a [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="a2436-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="a2436-108">I messaggi binari sono illeggibili quando si esaminano le tracce di rete e i log, a meno che i byte non vengano passati tramite un parser MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="a2436-109">SignalRdispone del supporto incorporato per il formato MessagePack e fornisce API per il client e il server da utilizzare.</span><span class="sxs-lookup"><span data-stu-id="a2436-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="a2436-110">Configurare MessagePack nel server</span><span class="sxs-lookup"><span data-stu-id="a2436-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="a2436-111">Per abilitare il protocollo dell'hub MessagePack nel server, installare il `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacchetto nell'app.</span><span class="sxs-lookup"><span data-stu-id="a2436-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="a2436-112">Nel `Startup.ConfigureServices` metodo aggiungere `AddMessagePackProtocol` alla `AddSignalR` chiamata per abilitare il supporto di MessagePack nel server.</span><span class="sxs-lookup"><span data-stu-id="a2436-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="a2436-113">JSON è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a2436-113">JSON is enabled by default.</span></span> <span data-ttu-id="a2436-114">L'aggiunta di MessagePack Abilita il supporto per i client JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="a2436-115">Per personalizzare il modo in cui MessagePack formatterà i dati, `AddMessagePackProtocol` accetta un delegato per la configurazione delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="a2436-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="a2436-116">In tale delegato, `SerializerOptions` è possibile usare la proprietà per configurare le opzioni di serializzazione MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="a2436-117">Per ulteriori informazioni sul funzionamento dei resolver, visitare la libreria MessagePack in [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="a2436-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="a2436-118">Gli attributi possono essere utilizzati per gli oggetti che si desidera serializzare per definire il modo in cui devono essere gestiti.</span><span class="sxs-lookup"><span data-stu-id="a2436-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="a2436-119">Si consiglia vivamente di esaminare [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e di applicare le patch consigliate.</span><span class="sxs-lookup"><span data-stu-id="a2436-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="a2436-120">Ad esempio, chiamando `.WithSecurity(MessagePackSecurity.UntrustedData)` quando si sostituisce `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="a2436-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="a2436-121">Configurare MessagePack nel client</span><span class="sxs-lookup"><span data-stu-id="a2436-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="a2436-122">JSON è abilitato per impostazione predefinita per i client supportati.</span><span class="sxs-lookup"><span data-stu-id="a2436-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="a2436-123">I client possono supportare un solo protocollo.</span><span class="sxs-lookup"><span data-stu-id="a2436-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="a2436-124">L'aggiunta del supporto di MessagePack sostituirà tutti i protocolli configurati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="a2436-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="a2436-125">Client .NET</span><span class="sxs-lookup"><span data-stu-id="a2436-125">.NET client</span></span>

<span data-ttu-id="a2436-126">Per abilitare MessagePack nel client .NET, installare il `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacchetto e chiamare il `AddMessagePackProtocol` `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a2436-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="a2436-127">Questa `AddMessagePackProtocol` chiamata accetta un delegato per la configurazione delle opzioni esattamente come il server.</span><span class="sxs-lookup"><span data-stu-id="a2436-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="a2436-128">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="a2436-128">JavaScript client</span></span>

<span data-ttu-id="a2436-129">Il supporto di MessagePack per il client JavaScript viene fornito dal [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pacchetto NPM.</span><span class="sxs-lookup"><span data-stu-id="a2436-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="a2436-130">Installare il pacchetto eseguendo il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="a2436-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="a2436-131">Dopo l'installazione del pacchetto NPM, il modulo può essere usato direttamente tramite un caricatore di moduli JavaScript o importato nel browser facendo riferimento al file seguente:</span><span class="sxs-lookup"><span data-stu-id="a2436-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="a2436-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="a2436-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="a2436-133">In un browser `msgpack5` è necessario fare riferimento anche alla raccolta.</span><span class="sxs-lookup"><span data-stu-id="a2436-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="a2436-134">Usare un `<script>` tag per creare un riferimento.</span><span class="sxs-lookup"><span data-stu-id="a2436-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="a2436-135">La libreria si trova in *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="a2436-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="a2436-136">Quando si usa l' `<script>` elemento, l'ordine è importante.</span><span class="sxs-lookup"><span data-stu-id="a2436-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="a2436-137">Se *signalr-protocol-msgpack.js* viene fatto riferimento prima *msgpack5.js*, si verifica un errore durante il tentativo di connessione a MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="a2436-138">*signalr.js* è necessario anche prima di *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="a2436-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="a2436-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` Se si aggiunge a, il client viene configurato per l'utilizzo del protocollo MessagePack durante la connessione a un server.</span><span class="sxs-lookup"><span data-stu-id="a2436-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="a2436-140">Al momento non sono disponibili opzioni di configurazione per il protocollo MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a2436-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="a2436-141">Peculiarità di MessagePack</span><span class="sxs-lookup"><span data-stu-id="a2436-141">MessagePack quirks</span></span>

<span data-ttu-id="a2436-142">Quando si usa il protocollo dell'hub MessagePack, è necessario tenere presenti alcuni aspetti.</span><span class="sxs-lookup"><span data-stu-id="a2436-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="a2436-143">MessagePack fa distinzione tra maiuscole e minuscole</span><span class="sxs-lookup"><span data-stu-id="a2436-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="a2436-144">Il protocollo MessagePack fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="a2436-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="a2436-145">Si consideri, ad esempio, la classe C# seguente:</span><span class="sxs-lookup"><span data-stu-id="a2436-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="a2436-146">Quando si invia dal client JavaScript, è necessario usare i `PascalCased` nomi delle proprietà, perché la combinazione di maiuscole e minuscole deve corrispondere esattamente alla classe C#.</span><span class="sxs-lookup"><span data-stu-id="a2436-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="a2436-147">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a2436-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="a2436-148">`camelCased`L'uso di nomi non verrà associato correttamente alla classe C#.</span><span class="sxs-lookup"><span data-stu-id="a2436-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="a2436-149">Per aggirare questo problema, è possibile usare l' `Key` attributo per specificare un nome diverso per la proprietà MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="a2436-150">Per ulteriori informazioni, vedere [la documentazione di MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="a2436-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="a2436-151">DateTime. Kind non viene mantenuto durante la serializzazione/deserializzazione</span><span class="sxs-lookup"><span data-stu-id="a2436-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="a2436-152">Il protocollo MessagePack non fornisce un modo per codificare il `Kind` valore di un oggetto `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="a2436-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="a2436-153">Di conseguenza, quando si deserializza una data, il protocollo dell'hub MessagePack viene convertito nel formato UTC se `DateTime.Kind` è `DateTimeKind.Local` altrimenti non toccherà il tempo e lo passerà così com'è.</span><span class="sxs-lookup"><span data-stu-id="a2436-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="a2436-154">Se si usano `DateTime` i valori, è consigliabile eseguire la conversione in formato UTC prima di inviarli.</span><span class="sxs-lookup"><span data-stu-id="a2436-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="a2436-155">Convertirli dall'ora UTC all'ora locale quando vengono ricevuti.</span><span class="sxs-lookup"><span data-stu-id="a2436-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="a2436-156">DateTime. MinValue non è supportato da MessagePack in JavaScript</span><span class="sxs-lookup"><span data-stu-id="a2436-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="a2436-157">La libreria [msgpack5](https://github.com/mcollina/msgpack5) usata dal SignalR client JavaScript non supporta il `timestamp96` tipo in MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="a2436-158">Questo tipo viene usato per codificare i valori di data molto grandi (molto presto in passato o molto lontano in futuro).</span><span class="sxs-lookup"><span data-stu-id="a2436-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="a2436-159">Il valore di `DateTime.MinValue` è `January 1, 0001` , che deve essere codificato in un `timestamp96` valore.</span><span class="sxs-lookup"><span data-stu-id="a2436-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="a2436-160">Per questo motivo, l'invio `DateTime.MinValue` a un client JavaScript non è supportato.</span><span class="sxs-lookup"><span data-stu-id="a2436-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="a2436-161">Quando `DateTime.MinValue` viene ricevuto dal client JavaScript, viene generato l'errore seguente:</span><span class="sxs-lookup"><span data-stu-id="a2436-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="a2436-162">In genere, `DateTime.MinValue` viene usato per codificare un valore "Missing" o `null` .</span><span class="sxs-lookup"><span data-stu-id="a2436-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="a2436-163">Se è necessario codificare tale valore in MessagePack, utilizzare un `DateTime` valore Nullable ( `DateTime?` ) o codificare un valore distinto che `bool` indica se la data è presente.</span><span class="sxs-lookup"><span data-stu-id="a2436-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="a2436-164">Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="a2436-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="a2436-165">Supporto di MessagePack nell'ambiente di compilazione "Ahead of Time"</span><span class="sxs-lookup"><span data-stu-id="a2436-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="a2436-166">La libreria [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilizzata dal client e dal server .NET utilizza la generazione del codice per ottimizzare la serializzazione.</span><span class="sxs-lookup"><span data-stu-id="a2436-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="a2436-167">Di conseguenza, non è supportato per impostazione predefinita negli ambienti che usano la compilazione "Ahead of Time" (ad esempio, Novell iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="a2436-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="a2436-168">È possibile usare MessagePack in questi ambienti mediante la "pre-generazione" del codice del serializzatore/deserializzatore.</span><span class="sxs-lookup"><span data-stu-id="a2436-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="a2436-169">Per ulteriori informazioni, vedere [la documentazione di MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="a2436-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="a2436-170">Una volta creati in precedenza i serializzatori, è possibile registrarli usando il delegato di configurazione passato a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="a2436-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="a2436-171">I controlli del tipo sono più severi in MessagePack</span><span class="sxs-lookup"><span data-stu-id="a2436-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="a2436-172">Il protocollo dell'hub JSON eseguirà le conversioni dei tipi durante la deserializzazione.</span><span class="sxs-lookup"><span data-stu-id="a2436-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="a2436-173">Se, ad esempio, l'oggetto in ingresso ha un valore della proprietà che è un numero ( `{ foo: 42 }` ), ma la proprietà nella classe .NET è di tipo `string` , il valore verrà convertito.</span><span class="sxs-lookup"><span data-stu-id="a2436-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="a2436-174">Tuttavia, MessagePack non esegue questa conversione e genererà un'eccezione che può essere visualizzata nei log lato server (e nella console):</span><span class="sxs-lookup"><span data-stu-id="a2436-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="a2436-175">Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="a2436-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="a2436-176">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="a2436-176">Related resources</span></span>

* [<span data-ttu-id="a2436-177">Introduzione</span><span class="sxs-lookup"><span data-stu-id="a2436-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a2436-178">Client .NET</span><span class="sxs-lookup"><span data-stu-id="a2436-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a2436-179">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="a2436-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="a2436-180">Questo articolo presuppone che il lettore abbia familiarità con gli argomenti trattati in [Introduzione](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="a2436-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="a2436-181">Che cos'è MessagePack?</span><span class="sxs-lookup"><span data-stu-id="a2436-181">What is MessagePack?</span></span>

<span data-ttu-id="a2436-182">[MessagePack](https://msgpack.org/index.html) è un formato di serializzazione binaria veloce e compatto.</span><span class="sxs-lookup"><span data-stu-id="a2436-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="a2436-183">È utile quando le prestazioni e la larghezza di banda rappresentano un problema perché crea messaggi più piccoli rispetto a [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="a2436-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="a2436-184">I messaggi binari sono illeggibili quando si esaminano le tracce di rete e i log, a meno che i byte non vengano passati tramite un parser MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="a2436-185">SignalRdispone del supporto incorporato per il formato MessagePack e fornisce le API per il client e il server da utilizzare.</span><span class="sxs-lookup"><span data-stu-id="a2436-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="a2436-186">Configurare MessagePack nel server</span><span class="sxs-lookup"><span data-stu-id="a2436-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="a2436-187">Per abilitare il protocollo dell'hub MessagePack nel server, installare il `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacchetto nell'app.</span><span class="sxs-lookup"><span data-stu-id="a2436-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="a2436-188">Nel `Startup.ConfigureServices` metodo aggiungere `AddMessagePackProtocol` alla `AddSignalR` chiamata per abilitare il supporto di MessagePack nel server.</span><span class="sxs-lookup"><span data-stu-id="a2436-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="a2436-189">JSON è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a2436-189">JSON is enabled by default.</span></span> <span data-ttu-id="a2436-190">L'aggiunta di MessagePack Abilita il supporto per i client JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="a2436-191">Per personalizzare il modo in cui MessagePack formatterà i dati, `AddMessagePackProtocol` accetta un delegato per la configurazione delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="a2436-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="a2436-192">In tale delegato, `FormatterResolvers` è possibile usare la proprietà per configurare le opzioni di serializzazione MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="a2436-193">Per ulteriori informazioni sul funzionamento dei resolver, visitare la libreria MessagePack in [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="a2436-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="a2436-194">Gli attributi possono essere utilizzati per gli oggetti che si desidera serializzare per definire il modo in cui devono essere gestiti.</span><span class="sxs-lookup"><span data-stu-id="a2436-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="a2436-195">Si consiglia vivamente di esaminare [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e di applicare le patch consigliate.</span><span class="sxs-lookup"><span data-stu-id="a2436-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="a2436-196">Ad esempio, impostando la `MessagePackSecurity.Active` proprietà statica su `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="a2436-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="a2436-197">L'impostazione `MessagePackSecurity.Active` di richiede l'installazione manuale [di una versione 1.9. x di MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="a2436-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="a2436-198">`MessagePack`L'installazione di 1.9. x aggiorna la versione SignalR utilizzata da.</span><span class="sxs-lookup"><span data-stu-id="a2436-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="a2436-199">Quando `MessagePackSecurity.Active` non è impostato su `MessagePackSecurity.UntrustedData` , un client dannoso potrebbe causare un attacco Denial of Service.</span><span class="sxs-lookup"><span data-stu-id="a2436-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="a2436-200">Impostare `MessagePackSecurity.Active` in `Program.Main` , come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="a2436-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="a2436-201">Configurare MessagePack nel client</span><span class="sxs-lookup"><span data-stu-id="a2436-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="a2436-202">JSON è abilitato per impostazione predefinita per i client supportati.</span><span class="sxs-lookup"><span data-stu-id="a2436-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="a2436-203">I client possono supportare un solo protocollo.</span><span class="sxs-lookup"><span data-stu-id="a2436-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="a2436-204">L'aggiunta del supporto di MessagePack sostituirà tutti i protocolli configurati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="a2436-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="a2436-205">Client .NET</span><span class="sxs-lookup"><span data-stu-id="a2436-205">.NET client</span></span>

<span data-ttu-id="a2436-206">Per abilitare MessagePack nel client .NET, installare il `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacchetto e chiamare il `AddMessagePackProtocol` `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a2436-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="a2436-207">Questa `AddMessagePackProtocol` chiamata accetta un delegato per la configurazione delle opzioni esattamente come il server.</span><span class="sxs-lookup"><span data-stu-id="a2436-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="a2436-208">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="a2436-208">JavaScript client</span></span>

<span data-ttu-id="a2436-209">Il supporto di MessagePack per il client JavaScript viene fornito dal [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pacchetto NPM.</span><span class="sxs-lookup"><span data-stu-id="a2436-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="a2436-210">Installare il pacchetto eseguendo il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="a2436-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="a2436-211">Dopo l'installazione del pacchetto NPM, il modulo può essere usato direttamente tramite un caricatore di moduli JavaScript o importato nel browser facendo riferimento al file seguente:</span><span class="sxs-lookup"><span data-stu-id="a2436-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="a2436-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="a2436-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="a2436-213">In un browser `msgpack5` è necessario fare riferimento anche alla raccolta.</span><span class="sxs-lookup"><span data-stu-id="a2436-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="a2436-214">Usare un `<script>` tag per creare un riferimento.</span><span class="sxs-lookup"><span data-stu-id="a2436-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="a2436-215">La libreria si trova in *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="a2436-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="a2436-216">Quando si usa l' `<script>` elemento, l'ordine è importante.</span><span class="sxs-lookup"><span data-stu-id="a2436-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="a2436-217">Se *signalr-protocol-msgpack.js* viene fatto riferimento prima *msgpack5.js*, si verifica un errore durante il tentativo di connessione a MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="a2436-218">*signalr.js* è necessario anche prima di *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="a2436-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="a2436-219">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` Se si aggiunge a, il client viene configurato per l'utilizzo del protocollo MessagePack durante la connessione a un server.</span><span class="sxs-lookup"><span data-stu-id="a2436-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="a2436-220">Al momento non sono disponibili opzioni di configurazione per il protocollo MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a2436-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="a2436-221">Peculiarità di MessagePack</span><span class="sxs-lookup"><span data-stu-id="a2436-221">MessagePack quirks</span></span>

<span data-ttu-id="a2436-222">Quando si usa il protocollo dell'hub MessagePack, è necessario tenere presenti alcuni aspetti.</span><span class="sxs-lookup"><span data-stu-id="a2436-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="a2436-223">MessagePack fa distinzione tra maiuscole e minuscole</span><span class="sxs-lookup"><span data-stu-id="a2436-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="a2436-224">Il protocollo MessagePack fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="a2436-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="a2436-225">Si consideri, ad esempio, la classe C# seguente:</span><span class="sxs-lookup"><span data-stu-id="a2436-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="a2436-226">Quando si invia dal client JavaScript, è necessario usare i `PascalCased` nomi delle proprietà, perché la combinazione di maiuscole e minuscole deve corrispondere esattamente alla classe C#.</span><span class="sxs-lookup"><span data-stu-id="a2436-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="a2436-227">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a2436-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="a2436-228">`camelCased`L'uso di nomi non verrà associato correttamente alla classe C#.</span><span class="sxs-lookup"><span data-stu-id="a2436-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="a2436-229">Per aggirare questo problema, è possibile usare l' `Key` attributo per specificare un nome diverso per la proprietà MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="a2436-230">Per ulteriori informazioni, vedere [la documentazione di MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="a2436-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="a2436-231">DateTime. Kind non viene mantenuto durante la serializzazione/deserializzazione</span><span class="sxs-lookup"><span data-stu-id="a2436-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="a2436-232">Il protocollo MessagePack non fornisce un modo per codificare il `Kind` valore di un oggetto `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="a2436-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="a2436-233">Di conseguenza, quando si deserializza una data, il protocollo dell'hub MessagePack presuppone che la data in ingresso sia in formato UTC.</span><span class="sxs-lookup"><span data-stu-id="a2436-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="a2436-234">Se si usano `DateTime` i valori nell'ora locale, è consigliabile eseguire la conversione in formato UTC prima di inviarli.</span><span class="sxs-lookup"><span data-stu-id="a2436-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="a2436-235">Convertirli dall'ora UTC all'ora locale quando vengono ricevuti.</span><span class="sxs-lookup"><span data-stu-id="a2436-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="a2436-236">Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="a2436-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="a2436-237">DateTime. MinValue non è supportato da MessagePack in JavaScript</span><span class="sxs-lookup"><span data-stu-id="a2436-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="a2436-238">La libreria [msgpack5](https://github.com/mcollina/msgpack5) usata dal SignalR client JavaScript non supporta il `timestamp96` tipo in MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="a2436-239">Questo tipo viene usato per codificare i valori di data molto grandi (molto presto in passato o molto lontano in futuro).</span><span class="sxs-lookup"><span data-stu-id="a2436-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="a2436-240">Il valore di `DateTime.MinValue` è `January 1, 0001` , che deve essere codificato in un `timestamp96` valore.</span><span class="sxs-lookup"><span data-stu-id="a2436-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="a2436-241">Per questo motivo, l'invio `DateTime.MinValue` a un client JavaScript non è supportato.</span><span class="sxs-lookup"><span data-stu-id="a2436-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="a2436-242">Quando `DateTime.MinValue` viene ricevuto dal client JavaScript, viene generato l'errore seguente:</span><span class="sxs-lookup"><span data-stu-id="a2436-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="a2436-243">In genere, `DateTime.MinValue` viene usato per codificare un valore "Missing" o `null` .</span><span class="sxs-lookup"><span data-stu-id="a2436-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="a2436-244">Se è necessario codificare tale valore in MessagePack, utilizzare un `DateTime` valore Nullable ( `DateTime?` ) o codificare un valore distinto che `bool` indica se la data è presente.</span><span class="sxs-lookup"><span data-stu-id="a2436-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="a2436-245">Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="a2436-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="a2436-246">Supporto di MessagePack nell'ambiente di compilazione "Ahead of Time"</span><span class="sxs-lookup"><span data-stu-id="a2436-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="a2436-247">La libreria [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizzata dal client e dal server .NET utilizza la generazione del codice per ottimizzare la serializzazione.</span><span class="sxs-lookup"><span data-stu-id="a2436-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="a2436-248">Di conseguenza, non è supportato per impostazione predefinita negli ambienti che usano la compilazione "Ahead of Time" (ad esempio, Novell iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="a2436-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="a2436-249">È possibile usare MessagePack in questi ambienti mediante la "pre-generazione" del codice del serializzatore/deserializzatore.</span><span class="sxs-lookup"><span data-stu-id="a2436-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="a2436-250">Per ulteriori informazioni, vedere [la documentazione di MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="a2436-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="a2436-251">Una volta creati in precedenza i serializzatori, è possibile registrarli usando il delegato di configurazione passato a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="a2436-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="a2436-252">I controlli del tipo sono più severi in MessagePack</span><span class="sxs-lookup"><span data-stu-id="a2436-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="a2436-253">Il protocollo dell'hub JSON eseguirà le conversioni dei tipi durante la deserializzazione.</span><span class="sxs-lookup"><span data-stu-id="a2436-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="a2436-254">Se, ad esempio, l'oggetto in ingresso ha un valore della proprietà che è un numero ( `{ foo: 42 }` ), ma la proprietà nella classe .NET è di tipo `string` , il valore verrà convertito.</span><span class="sxs-lookup"><span data-stu-id="a2436-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="a2436-255">Tuttavia, MessagePack non esegue questa conversione e genererà un'eccezione che può essere visualizzata nei log lato server (e nella console):</span><span class="sxs-lookup"><span data-stu-id="a2436-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="a2436-256">Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="a2436-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="a2436-257">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="a2436-257">Related resources</span></span>

* [<span data-ttu-id="a2436-258">Introduzione</span><span class="sxs-lookup"><span data-stu-id="a2436-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a2436-259">Client .NET</span><span class="sxs-lookup"><span data-stu-id="a2436-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a2436-260">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="a2436-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a2436-261">Questo articolo presuppone che il lettore abbia familiarità con gli argomenti trattati in [Introduzione](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="a2436-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="a2436-262">Che cos'è MessagePack?</span><span class="sxs-lookup"><span data-stu-id="a2436-262">What is MessagePack?</span></span>

<span data-ttu-id="a2436-263">[MessagePack](https://msgpack.org/index.html) è un formato di serializzazione binaria veloce e compatto.</span><span class="sxs-lookup"><span data-stu-id="a2436-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="a2436-264">È utile quando le prestazioni e la larghezza di banda rappresentano un problema perché crea messaggi più piccoli rispetto a [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="a2436-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="a2436-265">I messaggi binari sono illeggibili quando si esaminano le tracce di rete e i log, a meno che i byte non vengano passati tramite un parser MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="a2436-266">SignalRdispone del supporto incorporato per il formato MessagePack e fornisce le API per il client e il server da utilizzare.</span><span class="sxs-lookup"><span data-stu-id="a2436-266">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="a2436-267">Configurare MessagePack nel server</span><span class="sxs-lookup"><span data-stu-id="a2436-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="a2436-268">Per abilitare il protocollo dell'hub MessagePack nel server, installare il `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacchetto nell'app.</span><span class="sxs-lookup"><span data-stu-id="a2436-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="a2436-269">Nel `Startup.ConfigureServices` metodo aggiungere `AddMessagePackProtocol` alla `AddSignalR` chiamata per abilitare il supporto di MessagePack nel server.</span><span class="sxs-lookup"><span data-stu-id="a2436-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="a2436-270">JSON è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a2436-270">JSON is enabled by default.</span></span> <span data-ttu-id="a2436-271">L'aggiunta di MessagePack Abilita il supporto per i client JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="a2436-272">Per personalizzare il modo in cui MessagePack formatterà i dati, `AddMessagePackProtocol` accetta un delegato per la configurazione delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="a2436-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="a2436-273">In tale delegato, `FormatterResolvers` è possibile usare la proprietà per configurare le opzioni di serializzazione MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="a2436-274">Per ulteriori informazioni sul funzionamento dei resolver, visitare la libreria MessagePack in [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="a2436-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="a2436-275">Gli attributi possono essere utilizzati per gli oggetti che si desidera serializzare per definire il modo in cui devono essere gestiti.</span><span class="sxs-lookup"><span data-stu-id="a2436-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="a2436-276">Si consiglia vivamente di esaminare [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e di applicare le patch consigliate.</span><span class="sxs-lookup"><span data-stu-id="a2436-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="a2436-277">Ad esempio, impostando la `MessagePackSecurity.Active` proprietà statica su `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="a2436-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="a2436-278">L'impostazione `MessagePackSecurity.Active` di richiede l'installazione manuale [di una versione 1.9. x di MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="a2436-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="a2436-279">`MessagePack`L'installazione di 1.9. x aggiorna la versione SignalR utilizzata da.</span><span class="sxs-lookup"><span data-stu-id="a2436-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="a2436-280">Quando `MessagePackSecurity.Active` non è impostato su `MessagePackSecurity.UntrustedData` , un client dannoso potrebbe causare un attacco Denial of Service.</span><span class="sxs-lookup"><span data-stu-id="a2436-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="a2436-281">Impostare `MessagePackSecurity.Active` in `Program.Main` , come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="a2436-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="a2436-282">Configurare MessagePack nel client</span><span class="sxs-lookup"><span data-stu-id="a2436-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="a2436-283">JSON è abilitato per impostazione predefinita per i client supportati.</span><span class="sxs-lookup"><span data-stu-id="a2436-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="a2436-284">I client possono supportare un solo protocollo.</span><span class="sxs-lookup"><span data-stu-id="a2436-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="a2436-285">L'aggiunta del supporto di MessagePack sostituirà tutti i protocolli configurati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="a2436-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="a2436-286">Client .NET</span><span class="sxs-lookup"><span data-stu-id="a2436-286">.NET client</span></span>

<span data-ttu-id="a2436-287">Per abilitare MessagePack nel client .NET, installare il `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacchetto e chiamare il `AddMessagePackProtocol` `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a2436-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="a2436-288">Questa `AddMessagePackProtocol` chiamata accetta un delegato per la configurazione delle opzioni esattamente come il server.</span><span class="sxs-lookup"><span data-stu-id="a2436-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="a2436-289">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="a2436-289">JavaScript client</span></span>

<span data-ttu-id="a2436-290">Il supporto di MessagePack per il client JavaScript viene fornito dal [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) pacchetto NPM.</span><span class="sxs-lookup"><span data-stu-id="a2436-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="a2436-291">Installare il pacchetto eseguendo il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="a2436-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="a2436-292">Dopo l'installazione del pacchetto NPM, il modulo può essere usato direttamente tramite un caricatore di moduli JavaScript o importato nel browser facendo riferimento al file seguente:</span><span class="sxs-lookup"><span data-stu-id="a2436-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="a2436-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="a2436-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="a2436-294">In un browser `msgpack5` è necessario fare riferimento anche alla raccolta.</span><span class="sxs-lookup"><span data-stu-id="a2436-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="a2436-295">Usare un `<script>` tag per creare un riferimento.</span><span class="sxs-lookup"><span data-stu-id="a2436-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="a2436-296">La libreria si trova in *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="a2436-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="a2436-297">Quando si usa l' `<script>` elemento, l'ordine è importante.</span><span class="sxs-lookup"><span data-stu-id="a2436-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="a2436-298">Se *signalr-protocol-msgpack.js* viene fatto riferimento prima *msgpack5.js*, si verifica un errore durante il tentativo di connessione a MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="a2436-299">*signalr.js* è necessario anche prima di *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="a2436-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="a2436-300">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` Se si aggiunge a, il client viene configurato per l'utilizzo del protocollo MessagePack durante la connessione a un server.</span><span class="sxs-lookup"><span data-stu-id="a2436-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="a2436-301">Al momento non sono disponibili opzioni di configurazione per il protocollo MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a2436-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="a2436-302">Peculiarità di MessagePack</span><span class="sxs-lookup"><span data-stu-id="a2436-302">MessagePack quirks</span></span>

<span data-ttu-id="a2436-303">Quando si usa il protocollo dell'hub MessagePack, è necessario tenere presenti alcuni aspetti.</span><span class="sxs-lookup"><span data-stu-id="a2436-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="a2436-304">MessagePack fa distinzione tra maiuscole e minuscole</span><span class="sxs-lookup"><span data-stu-id="a2436-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="a2436-305">Il protocollo MessagePack fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="a2436-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="a2436-306">Si consideri, ad esempio, la classe C# seguente:</span><span class="sxs-lookup"><span data-stu-id="a2436-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="a2436-307">Quando si invia dal client JavaScript, è necessario usare i `PascalCased` nomi delle proprietà, perché la combinazione di maiuscole e minuscole deve corrispondere esattamente alla classe C#.</span><span class="sxs-lookup"><span data-stu-id="a2436-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="a2436-308">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a2436-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="a2436-309">`camelCased`L'uso di nomi non verrà associato correttamente alla classe C#.</span><span class="sxs-lookup"><span data-stu-id="a2436-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="a2436-310">Per aggirare questo problema, è possibile usare l' `Key` attributo per specificare un nome diverso per la proprietà MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="a2436-311">Per ulteriori informazioni, vedere [la documentazione di MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="a2436-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="a2436-312">DateTime. Kind non viene mantenuto durante la serializzazione/deserializzazione</span><span class="sxs-lookup"><span data-stu-id="a2436-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="a2436-313">Il protocollo MessagePack non fornisce un modo per codificare il `Kind` valore di un oggetto `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="a2436-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="a2436-314">Di conseguenza, quando si deserializza una data, il protocollo dell'hub MessagePack presuppone che la data in ingresso sia in formato UTC.</span><span class="sxs-lookup"><span data-stu-id="a2436-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="a2436-315">Se si usano `DateTime` i valori nell'ora locale, è consigliabile eseguire la conversione in formato UTC prima di inviarli.</span><span class="sxs-lookup"><span data-stu-id="a2436-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="a2436-316">Convertirli dall'ora UTC all'ora locale quando vengono ricevuti.</span><span class="sxs-lookup"><span data-stu-id="a2436-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="a2436-317">Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="a2436-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="a2436-318">DateTime. MinValue non è supportato da MessagePack in JavaScript</span><span class="sxs-lookup"><span data-stu-id="a2436-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="a2436-319">La libreria [msgpack5](https://github.com/mcollina/msgpack5) usata dal SignalR client JavaScript non supporta il `timestamp96` tipo in MessagePack.</span><span class="sxs-lookup"><span data-stu-id="a2436-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="a2436-320">Questo tipo viene usato per codificare i valori di data molto grandi (molto presto in passato o molto lontano in futuro).</span><span class="sxs-lookup"><span data-stu-id="a2436-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="a2436-321">Il valore di `DateTime.MinValue` è `January 1, 0001` che deve essere codificato in un `timestamp96` valore.</span><span class="sxs-lookup"><span data-stu-id="a2436-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="a2436-322">Per questo motivo, l'invio `DateTime.MinValue` a un client JavaScript non è supportato.</span><span class="sxs-lookup"><span data-stu-id="a2436-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="a2436-323">Quando `DateTime.MinValue` viene ricevuto dal client JavaScript, viene generato l'errore seguente:</span><span class="sxs-lookup"><span data-stu-id="a2436-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="a2436-324">In genere, `DateTime.MinValue` viene usato per codificare un valore "Missing" o `null` .</span><span class="sxs-lookup"><span data-stu-id="a2436-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="a2436-325">Se è necessario codificare tale valore in MessagePack, utilizzare un `DateTime` valore Nullable ( `DateTime?` ) o codificare un valore distinto che `bool` indica se la data è presente.</span><span class="sxs-lookup"><span data-stu-id="a2436-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="a2436-326">Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="a2436-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="a2436-327">Supporto di MessagePack nell'ambiente di compilazione "Ahead of Time"</span><span class="sxs-lookup"><span data-stu-id="a2436-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="a2436-328">La libreria [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizzata dal client e dal server .NET utilizza la generazione del codice per ottimizzare la serializzazione.</span><span class="sxs-lookup"><span data-stu-id="a2436-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="a2436-329">Di conseguenza, non è supportato per impostazione predefinita negli ambienti che usano la compilazione "Ahead of Time" (ad esempio, Novell iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="a2436-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="a2436-330">È possibile usare MessagePack in questi ambienti mediante la "pre-generazione" del codice del serializzatore/deserializzatore.</span><span class="sxs-lookup"><span data-stu-id="a2436-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="a2436-331">Per ulteriori informazioni, vedere [la documentazione di MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="a2436-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="a2436-332">Una volta creati in precedenza i serializzatori, è possibile registrarli usando il delegato di configurazione passato a `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="a2436-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="a2436-333">I controlli del tipo sono più severi in MessagePack</span><span class="sxs-lookup"><span data-stu-id="a2436-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="a2436-334">Il protocollo dell'hub JSON eseguirà le conversioni dei tipi durante la deserializzazione.</span><span class="sxs-lookup"><span data-stu-id="a2436-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="a2436-335">Se, ad esempio, l'oggetto in ingresso ha un valore della proprietà che è un numero ( `{ foo: 42 }` ), ma la proprietà nella classe .NET è di tipo `string` , il valore verrà convertito.</span><span class="sxs-lookup"><span data-stu-id="a2436-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="a2436-336">Tuttavia, MessagePack non esegue questa conversione e genererà un'eccezione che può essere visualizzata nei log lato server (e nella console):</span><span class="sxs-lookup"><span data-stu-id="a2436-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="a2436-337">Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="a2436-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="a2436-338">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="a2436-338">Related resources</span></span>

* [<span data-ttu-id="a2436-339">Introduzione</span><span class="sxs-lookup"><span data-stu-id="a2436-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a2436-340">Client .NET</span><span class="sxs-lookup"><span data-stu-id="a2436-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a2436-341">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="a2436-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
