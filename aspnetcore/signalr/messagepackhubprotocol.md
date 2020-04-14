---
title: Usare il protocollo SignalR hub MessagePack in ASP.NET CoreUse MessagePack Hub Protocol in for ASP.NET Core
author: bradygaster
description: Aggiungere il protocollo Hub SignalRMessagePack a ASP.NET Core .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277236"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="49af6-103">Usare il protocollo SignalR hub MessagePack in ASP.NET CoreUse MessagePack Hub Protocol in for ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49af6-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="49af6-104">In questo articolo si presuppone che il lettore abbia familiarità con gli argomenti trattati in [Introduzione](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="49af6-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="49af6-105">Che cos'è MessagePack?</span><span class="sxs-lookup"><span data-stu-id="49af6-105">What is MessagePack?</span></span>

<span data-ttu-id="49af6-106">[MessagePack](https://msgpack.org/index.html) è un formato di serializzazione binaria veloce e compatto.</span><span class="sxs-lookup"><span data-stu-id="49af6-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="49af6-107">È utile quando le prestazioni e la larghezza di banda sono un problema perché crea messaggi più piccoli rispetto a [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="49af6-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="49af6-108">I messaggi binari sono illeggibili quando si esaminano le tracce di rete e i registri, a meno che i byte non vengano passati tramite un parser MessagePack.</span><span class="sxs-lookup"><span data-stu-id="49af6-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="49af6-109">dispone del supporto incorporato per il formato MessagePack e fornisce API per il client e il server da utilizzare.</span><span class="sxs-lookup"><span data-stu-id="49af6-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="49af6-110">Configurare MessagePack sul server</span><span class="sxs-lookup"><span data-stu-id="49af6-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="49af6-111">Per abilitare il protocollo Hub MessagePack `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` nel server, installa il pacchetto nell'app.</span><span class="sxs-lookup"><span data-stu-id="49af6-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="49af6-112">Nel `Startup.ConfigureServices` metodo aggiungere `AddMessagePackProtocol` alla `AddSignalR` chiamata per abilitare il supporto MessagePack sul server.</span><span class="sxs-lookup"><span data-stu-id="49af6-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="49af6-113">JSON è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="49af6-113">JSON is enabled by default.</span></span> <span data-ttu-id="49af6-114">L'aggiunta di MessagePack consente il supporto per i client JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="49af6-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="49af6-115">Per personalizzare il modo in `AddMessagePackProtocol` cui MessagePack formatterà i dati, accetta un delegato per la configurazione delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="49af6-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="49af6-116">In tale delegato, la proprietà può essere utilizzata per configurare le opzioni di serializzazione MessagePack.In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span><span class="sxs-lookup"><span data-stu-id="49af6-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="49af6-117">Per ulteriori informazioni sul funzionamento dei sistemi di risoluzione, visitare la libreria MessagePack in [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="49af6-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="49af6-118">Gli attributi possono essere utilizzati sugli oggetti che si desidera serializzare per definire la modalità di gestione da parte degli attributi.</span><span class="sxs-lookup"><span data-stu-id="49af6-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="49af6-119">Si consiglia vivamente di esaminare [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e applicare le patch consigliate.</span><span class="sxs-lookup"><span data-stu-id="49af6-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="49af6-120">Ad esempio, `.WithSecurity(MessagePackSecurity.UntrustedData)` chiamando quando `SerializerOptions`si sostituisce il file .</span><span class="sxs-lookup"><span data-stu-id="49af6-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="49af6-121">Configurare MessagePack sul client</span><span class="sxs-lookup"><span data-stu-id="49af6-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="49af6-122">JSON è abilitato per impostazione predefinita per i client supportati.</span><span class="sxs-lookup"><span data-stu-id="49af6-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="49af6-123">I client possono supportare un solo protocollo.</span><span class="sxs-lookup"><span data-stu-id="49af6-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="49af6-124">L'aggiunta del supporto MessagePack sostituirà tutti i protocolli configurati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="49af6-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="49af6-125">Client .NET</span><span class="sxs-lookup"><span data-stu-id="49af6-125">.NET client</span></span>

<span data-ttu-id="49af6-126">Per abilitare MessagePack nel client `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`installare il pacchetto e chiamare su .</span><span class="sxs-lookup"><span data-stu-id="49af6-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="49af6-127">Questa `AddMessagePackProtocol` chiamata accetta un delegato per la configurazione delle opzioni proprio come il server.</span><span class="sxs-lookup"><span data-stu-id="49af6-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="49af6-128">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="49af6-128">JavaScript client</span></span>

<span data-ttu-id="49af6-129">Il supporto MessagePack per il client [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) JavaScript viene fornito dal pacchetto npm.</span><span class="sxs-lookup"><span data-stu-id="49af6-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="49af6-130">Installare il pacchetto eseguendo il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="49af6-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="49af6-131">Dopo aver installato il pacchetto npm, il modulo può essere utilizzato direttamente tramite un caricatore di moduli JavaScript o importato nel browser facendo riferimento al seguente file:</span><span class="sxs-lookup"><span data-stu-id="49af6-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="49af6-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="49af6-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="49af6-133">In un browser, è necessario fare riferimento anche alla `msgpack5` libreria.</span><span class="sxs-lookup"><span data-stu-id="49af6-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="49af6-134">Utilizzare `<script>` un tag per creare un riferimento.</span><span class="sxs-lookup"><span data-stu-id="49af6-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="49af6-135">La libreria è reperibile in *node_modules.*</span><span class="sxs-lookup"><span data-stu-id="49af6-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="49af6-136">Quando si `<script>` utilizza l'elemento, l'ordine è importante.</span><span class="sxs-lookup"><span data-stu-id="49af6-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="49af6-137">Se si fa riferimento a *signalr-protocol-msgpack.js* prima di *msgpack5.js*, si verifica un errore quando si tenta di connettersi con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="49af6-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="49af6-138">*signalr.js* è necessario anche prima *di signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="49af6-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="49af6-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` L'aggiunta `HubConnectionBuilder` al client configurerà il client per l'utilizzo del protocollo MessagePack per la connessione a un server.</span><span class="sxs-lookup"><span data-stu-id="49af6-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="49af6-140">Al momento, non sono disponibili opzioni di configurazione per il protocollo MessagePack sul client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="49af6-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="49af6-141">Stranezze di MessagePack</span><span class="sxs-lookup"><span data-stu-id="49af6-141">MessagePack quirks</span></span>

<span data-ttu-id="49af6-142">Quando si utilizza il protocollo Hub MessagePack, è necessario tenere presente alcuni problemi.</span><span class="sxs-lookup"><span data-stu-id="49af6-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="49af6-143">MessagePack fa distinzione tra maiuscole e minuscole</span><span class="sxs-lookup"><span data-stu-id="49af6-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="49af6-144">Il protocollo MessagePack fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="49af6-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="49af6-145">Si consideri, ad esempio, la seguente classe di C:</span><span class="sxs-lookup"><span data-stu-id="49af6-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="49af6-146">Quando si invia dal client JavaScript, è necessario utilizzare `PascalCased` i nomi delle proprietà, poiché l'uso di maiuscole e minuscole deve corrispondere esattamente alla classe di C.</span><span class="sxs-lookup"><span data-stu-id="49af6-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="49af6-147">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="49af6-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="49af6-148">L'utilizzo di `camelCased` nomi non verrà associato correttamente alla classe C.</span><span class="sxs-lookup"><span data-stu-id="49af6-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="49af6-149">È possibile risolvere questo `Key` problema utilizzando l'attributo per specificare un nome diverso per il MessagePack proprietà.</span><span class="sxs-lookup"><span data-stu-id="49af6-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="49af6-150">Per ulteriori informazioni, vedere la documentazione di [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="49af6-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="49af6-151">DateTime.Kind non viene mantenuto durante la serializzazione/deserializzazione</span><span class="sxs-lookup"><span data-stu-id="49af6-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="49af6-152">Il protocollo MessagePack non fornisce un modo `Kind` per `DateTime`codificare il valore di un oggetto .</span><span class="sxs-lookup"><span data-stu-id="49af6-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="49af6-153">Di conseguenza, quando si deserializza una data, il protocollo `DateTime.Kind` Hub `DateTimeKind.Local` MessagePack verrà convertito nel formato UTC se il è in caso contrario non toccherà l'ora e lo passerà così com'è.</span><span class="sxs-lookup"><span data-stu-id="49af6-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="49af6-154">Se si utilizzano `DateTime` valori, è consigliabile eseguire la conversione in UTC prima di inviarli.</span><span class="sxs-lookup"><span data-stu-id="49af6-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="49af6-155">Convertirli dall'ora UTC all'ora locale quando li ricevi.</span><span class="sxs-lookup"><span data-stu-id="49af6-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="49af6-156">DateTime.MinValue non è supportato da MessagePack in JavaScript</span><span class="sxs-lookup"><span data-stu-id="49af6-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="49af6-157">La libreria [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilizzata dal client JavaScript `timestamp96` non supporta il tipo in MessagePack.</span><span class="sxs-lookup"><span data-stu-id="49af6-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="49af6-158">Questo tipo viene utilizzato per codificare valori di data molto grandi (sia molto presto nel passato o molto lontano in futuro).</span><span class="sxs-lookup"><span data-stu-id="49af6-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="49af6-159">Il valore `DateTime.MinValue` `January 1, 0001`di è , che `timestamp96` deve essere codificato in un valore.</span><span class="sxs-lookup"><span data-stu-id="49af6-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="49af6-160">Per questo motivo, l'invio `DateTime.MinValue` a un client JavaScript non è supportato.</span><span class="sxs-lookup"><span data-stu-id="49af6-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="49af6-161">Quando `DateTime.MinValue` viene ricevuto dal client JavaScript, viene generato il seguente errore:</span><span class="sxs-lookup"><span data-stu-id="49af6-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="49af6-162">Di `DateTime.MinValue` solito, viene utilizzato per codificare un "mancante" o `null` un valore.</span><span class="sxs-lookup"><span data-stu-id="49af6-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="49af6-163">Se è necessario codificare tale valore in MessagePack,`DateTime?`utilizzare un valore `bool` nullable `DateTime` ( ) o codificare un valore separato che indica se la data è presente.</span><span class="sxs-lookup"><span data-stu-id="49af6-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="49af6-164">Per ulteriori informazioni su questa limitazione, vedere Problema di GitHub [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="49af6-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="49af6-165">Supporto MessagePack nell'ambiente di compilazione "ahead-of-time"</span><span class="sxs-lookup"><span data-stu-id="49af6-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="49af6-166">La libreria [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilizzata dal client e dal server .NET utilizza la generazione di codice per ottimizzare la serializzazione.</span><span class="sxs-lookup"><span data-stu-id="49af6-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="49af6-167">Di conseguenza, non è supportato per impostazione predefinita in ambienti che utilizzano la compilazione "ahead-of-time" (ad esempio Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="49af6-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="49af6-168">È possibile utilizzare MessagePack in questi ambienti "pre-generando" il codice del serializzatore/deserializzatore.</span><span class="sxs-lookup"><span data-stu-id="49af6-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="49af6-169">Per ulteriori informazioni, vedere la documentazione di [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="49af6-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="49af6-170">Dopo aver pregenerato i serializzatori, è possibile registrarli `AddMessagePackProtocol`utilizzando il delegato di configurazione passato a :</span><span class="sxs-lookup"><span data-stu-id="49af6-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="49af6-171">I controlli dei tipi sono più rigorosi in MessagePackType checks are more strict in MessagePack</span><span class="sxs-lookup"><span data-stu-id="49af6-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="49af6-172">Il protocollo JSON Hub eseguirà le conversioni dei tipi durante la deserializzazione.</span><span class="sxs-lookup"><span data-stu-id="49af6-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="49af6-173">Ad esempio, se l'oggetto in ingresso dispone`{ foo: 42 }`di un valore di proprietà che `string`è un numero ( ) ma la proprietà nella classe .NET è di tipo , il valore verrà convertito.</span><span class="sxs-lookup"><span data-stu-id="49af6-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="49af6-174">Tuttavia, MessagePack non esegue questa conversione e genererà un'eccezione che può essere visualizzata nei log sul lato server (e nella console):However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span><span class="sxs-lookup"><span data-stu-id="49af6-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="49af6-175">Per ulteriori informazioni su questa limitazione, vedere Problema Di GitHub [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="49af6-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="49af6-176">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="49af6-176">Related resources</span></span>

* [<span data-ttu-id="49af6-177">Guida introduttiva</span><span class="sxs-lookup"><span data-stu-id="49af6-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="49af6-178">Client .NET</span><span class="sxs-lookup"><span data-stu-id="49af6-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="49af6-179">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="49af6-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="49af6-180">In questo articolo si presuppone che il lettore abbia familiarità con gli argomenti trattati in [Introduzione](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="49af6-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="49af6-181">Che cos'è MessagePack?</span><span class="sxs-lookup"><span data-stu-id="49af6-181">What is MessagePack?</span></span>

<span data-ttu-id="49af6-182">[MessagePack](https://msgpack.org/index.html) è un formato di serializzazione binaria veloce e compatto.</span><span class="sxs-lookup"><span data-stu-id="49af6-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="49af6-183">È utile quando le prestazioni e la larghezza di banda sono un problema perché crea messaggi più piccoli rispetto a [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="49af6-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="49af6-184">I messaggi binari sono illeggibili quando si esaminano le tracce di rete e i registri, a meno che i byte non vengano passati tramite un parser MessagePack.</span><span class="sxs-lookup"><span data-stu-id="49af6-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="49af6-185">include il supporto incorporato per il formato MessagePack e fornisce API per il client e il server da utilizzare.</span><span class="sxs-lookup"><span data-stu-id="49af6-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="49af6-186">Configurare MessagePack sul server</span><span class="sxs-lookup"><span data-stu-id="49af6-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="49af6-187">Per abilitare il protocollo Hub MessagePack `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` nel server, installa il pacchetto nell'app.</span><span class="sxs-lookup"><span data-stu-id="49af6-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="49af6-188">Nel `Startup.ConfigureServices` metodo aggiungere `AddMessagePackProtocol` alla `AddSignalR` chiamata per abilitare il supporto MessagePack sul server.</span><span class="sxs-lookup"><span data-stu-id="49af6-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="49af6-189">JSON è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="49af6-189">JSON is enabled by default.</span></span> <span data-ttu-id="49af6-190">L'aggiunta di MessagePack consente il supporto per i client JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="49af6-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="49af6-191">Per personalizzare il modo in `AddMessagePackProtocol` cui MessagePack formatterà i dati, accetta un delegato per la configurazione delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="49af6-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="49af6-192">In tale delegato, la proprietà può essere utilizzata per configurare le opzioni di serializzazione MessagePack.In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span><span class="sxs-lookup"><span data-stu-id="49af6-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="49af6-193">Per ulteriori informazioni sul funzionamento dei sistemi di risoluzione, visitare la libreria MessagePack in [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="49af6-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="49af6-194">Gli attributi possono essere utilizzati sugli oggetti che si desidera serializzare per definire la modalità di gestione da parte degli attributi.</span><span class="sxs-lookup"><span data-stu-id="49af6-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="49af6-195">Si consiglia vivamente di esaminare [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e applicare le patch consigliate.</span><span class="sxs-lookup"><span data-stu-id="49af6-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="49af6-196">Ad esempio, `MessagePackSecurity.Active` l'impostazione della proprietà static su `MessagePackSecurity.UntrustedData`.</span><span class="sxs-lookup"><span data-stu-id="49af6-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="49af6-197">L'impostazione di richiede l'installazione `MessagePackSecurity.Active` manuale di una versione [1.9.x di MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="49af6-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="49af6-198">L'installazione di `MessagePack` 1.9.x SignalR aggiorna la versione.</span><span class="sxs-lookup"><span data-stu-id="49af6-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="49af6-199">Quando `MessagePackSecurity.Active` non è `MessagePackSecurity.UntrustedData`impostato su , un client dannoso potrebbe causare un Denial of Service.</span><span class="sxs-lookup"><span data-stu-id="49af6-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="49af6-200">Impostato `MessagePackSecurity.Active` `Program.Main`in , come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="49af6-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="49af6-201">Configurare MessagePack sul client</span><span class="sxs-lookup"><span data-stu-id="49af6-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="49af6-202">JSON è abilitato per impostazione predefinita per i client supportati.</span><span class="sxs-lookup"><span data-stu-id="49af6-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="49af6-203">I client possono supportare un solo protocollo.</span><span class="sxs-lookup"><span data-stu-id="49af6-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="49af6-204">L'aggiunta del supporto MessagePack sostituirà tutti i protocolli configurati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="49af6-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="49af6-205">Client .NET</span><span class="sxs-lookup"><span data-stu-id="49af6-205">.NET client</span></span>

<span data-ttu-id="49af6-206">Per abilitare MessagePack nel client `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`installare il pacchetto e chiamare su .</span><span class="sxs-lookup"><span data-stu-id="49af6-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="49af6-207">Questa `AddMessagePackProtocol` chiamata accetta un delegato per la configurazione delle opzioni proprio come il server.</span><span class="sxs-lookup"><span data-stu-id="49af6-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="49af6-208">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="49af6-208">JavaScript client</span></span>

<span data-ttu-id="49af6-209">Il supporto MessagePack per il client [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) JavaScript viene fornito dal pacchetto npm.</span><span class="sxs-lookup"><span data-stu-id="49af6-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="49af6-210">Installare il pacchetto eseguendo il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="49af6-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="49af6-211">Dopo aver installato il pacchetto npm, il modulo può essere utilizzato direttamente tramite un caricatore di moduli JavaScript o importato nel browser facendo riferimento al seguente file:</span><span class="sxs-lookup"><span data-stu-id="49af6-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="49af6-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="49af6-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="49af6-213">In un browser, è necessario fare riferimento anche alla `msgpack5` libreria.</span><span class="sxs-lookup"><span data-stu-id="49af6-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="49af6-214">Utilizzare `<script>` un tag per creare un riferimento.</span><span class="sxs-lookup"><span data-stu-id="49af6-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="49af6-215">La libreria è reperibile in *node_modules.*</span><span class="sxs-lookup"><span data-stu-id="49af6-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="49af6-216">Quando si `<script>` utilizza l'elemento, l'ordine è importante.</span><span class="sxs-lookup"><span data-stu-id="49af6-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="49af6-217">Se si fa riferimento a *signalr-protocol-msgpack.js* prima di *msgpack5.js*, si verifica un errore quando si tenta di connettersi con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="49af6-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="49af6-218">*signalr.js* è necessario anche prima *di signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="49af6-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="49af6-219">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` L'aggiunta `HubConnectionBuilder` al client configurerà il client per l'utilizzo del protocollo MessagePack per la connessione a un server.</span><span class="sxs-lookup"><span data-stu-id="49af6-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="49af6-220">Al momento, non sono disponibili opzioni di configurazione per il protocollo MessagePack sul client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="49af6-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="49af6-221">Stranezze di MessagePack</span><span class="sxs-lookup"><span data-stu-id="49af6-221">MessagePack quirks</span></span>

<span data-ttu-id="49af6-222">Quando si utilizza il protocollo Hub MessagePack, è necessario tenere presente alcuni problemi.</span><span class="sxs-lookup"><span data-stu-id="49af6-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="49af6-223">MessagePack fa distinzione tra maiuscole e minuscole</span><span class="sxs-lookup"><span data-stu-id="49af6-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="49af6-224">Il protocollo MessagePack fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="49af6-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="49af6-225">Si consideri, ad esempio, la seguente classe di C:</span><span class="sxs-lookup"><span data-stu-id="49af6-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="49af6-226">Quando si invia dal client JavaScript, è necessario utilizzare `PascalCased` i nomi delle proprietà, poiché l'uso di maiuscole e minuscole deve corrispondere esattamente alla classe di C.</span><span class="sxs-lookup"><span data-stu-id="49af6-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="49af6-227">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="49af6-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="49af6-228">L'utilizzo di `camelCased` nomi non verrà associato correttamente alla classe C.</span><span class="sxs-lookup"><span data-stu-id="49af6-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="49af6-229">È possibile risolvere questo `Key` problema utilizzando l'attributo per specificare un nome diverso per il MessagePack proprietà.</span><span class="sxs-lookup"><span data-stu-id="49af6-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="49af6-230">Per ulteriori informazioni, vedere la documentazione di [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="49af6-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="49af6-231">DateTime.Kind non viene mantenuto durante la serializzazione/deserializzazione</span><span class="sxs-lookup"><span data-stu-id="49af6-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="49af6-232">Il protocollo MessagePack non fornisce un modo `Kind` per `DateTime`codificare il valore di un oggetto .</span><span class="sxs-lookup"><span data-stu-id="49af6-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="49af6-233">Di conseguenza, durante la deserializzazione di una data, il protocollo Hub MessagePack presuppone che la data in ingresso sia in formato UTC.</span><span class="sxs-lookup"><span data-stu-id="49af6-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="49af6-234">Se si utilizzano `DateTime` valori nell'ora locale, è consigliabile eseguire la conversione in UTC prima di inviarli.</span><span class="sxs-lookup"><span data-stu-id="49af6-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="49af6-235">Convertirli dall'ora UTC all'ora locale quando li ricevi.</span><span class="sxs-lookup"><span data-stu-id="49af6-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="49af6-236">Per ulteriori informazioni su questa limitazione, vedere Problema di GitHub [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="49af6-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="49af6-237">DateTime.MinValue non è supportato da MessagePack in JavaScript</span><span class="sxs-lookup"><span data-stu-id="49af6-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="49af6-238">La libreria [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilizzata dal client JavaScript `timestamp96` non supporta il tipo in MessagePack.</span><span class="sxs-lookup"><span data-stu-id="49af6-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="49af6-239">Questo tipo viene utilizzato per codificare valori di data molto grandi (sia molto presto nel passato o molto lontano in futuro).</span><span class="sxs-lookup"><span data-stu-id="49af6-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="49af6-240">Il valore `DateTime.MinValue` `January 1, 0001`di è , che `timestamp96` deve essere codificato in un valore.</span><span class="sxs-lookup"><span data-stu-id="49af6-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="49af6-241">Per questo motivo, l'invio `DateTime.MinValue` a un client JavaScript non è supportato.</span><span class="sxs-lookup"><span data-stu-id="49af6-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="49af6-242">Quando `DateTime.MinValue` viene ricevuto dal client JavaScript, viene generato il seguente errore:</span><span class="sxs-lookup"><span data-stu-id="49af6-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="49af6-243">Di `DateTime.MinValue` solito, viene utilizzato per codificare un "mancante" o `null` un valore.</span><span class="sxs-lookup"><span data-stu-id="49af6-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="49af6-244">Se è necessario codificare tale valore in MessagePack,`DateTime?`utilizzare un valore `bool` nullable `DateTime` ( ) o codificare un valore separato che indica se la data è presente.</span><span class="sxs-lookup"><span data-stu-id="49af6-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="49af6-245">Per ulteriori informazioni su questa limitazione, vedere Problema di GitHub [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="49af6-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="49af6-246">Supporto MessagePack nell'ambiente di compilazione "ahead-of-time"</span><span class="sxs-lookup"><span data-stu-id="49af6-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="49af6-247">La libreria [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizzata dal client e dal server .NET utilizza la generazione di codice per ottimizzare la serializzazione.</span><span class="sxs-lookup"><span data-stu-id="49af6-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="49af6-248">Di conseguenza, non è supportato per impostazione predefinita in ambienti che utilizzano la compilazione "ahead-of-time" (ad esempio Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="49af6-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="49af6-249">È possibile utilizzare MessagePack in questi ambienti "pre-generando" il codice del serializzatore/deserializzatore.</span><span class="sxs-lookup"><span data-stu-id="49af6-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="49af6-250">Per ulteriori informazioni, vedere la documentazione di [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="49af6-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="49af6-251">Dopo aver pregenerato i serializzatori, è possibile registrarli `AddMessagePackProtocol`utilizzando il delegato di configurazione passato a :</span><span class="sxs-lookup"><span data-stu-id="49af6-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="49af6-252">I controlli dei tipi sono più rigorosi in MessagePackType checks are more strict in MessagePack</span><span class="sxs-lookup"><span data-stu-id="49af6-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="49af6-253">Il protocollo JSON Hub eseguirà le conversioni dei tipi durante la deserializzazione.</span><span class="sxs-lookup"><span data-stu-id="49af6-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="49af6-254">Ad esempio, se l'oggetto in ingresso dispone`{ foo: 42 }`di un valore di proprietà che `string`è un numero ( ) ma la proprietà nella classe .NET è di tipo , il valore verrà convertito.</span><span class="sxs-lookup"><span data-stu-id="49af6-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="49af6-255">Tuttavia, MessagePack non esegue questa conversione e genererà un'eccezione che può essere visualizzata nei log sul lato server (e nella console):However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span><span class="sxs-lookup"><span data-stu-id="49af6-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="49af6-256">Per ulteriori informazioni su questa limitazione, vedere Problema Di GitHub [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="49af6-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="49af6-257">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="49af6-257">Related resources</span></span>

* [<span data-ttu-id="49af6-258">Guida introduttiva</span><span class="sxs-lookup"><span data-stu-id="49af6-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="49af6-259">Client .NET</span><span class="sxs-lookup"><span data-stu-id="49af6-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="49af6-260">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="49af6-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="49af6-261">In questo articolo si presuppone che il lettore abbia familiarità con gli argomenti trattati in [Introduzione](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="49af6-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="49af6-262">Che cos'è MessagePack?</span><span class="sxs-lookup"><span data-stu-id="49af6-262">What is MessagePack?</span></span>

<span data-ttu-id="49af6-263">[MessagePack](https://msgpack.org/index.html) è un formato di serializzazione binaria veloce e compatto.</span><span class="sxs-lookup"><span data-stu-id="49af6-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="49af6-264">È utile quando le prestazioni e la larghezza di banda sono un problema perché crea messaggi più piccoli rispetto a [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="49af6-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="49af6-265">I messaggi binari sono illeggibili quando si esaminano le tracce di rete e i registri, a meno che i byte non vengano passati tramite un parser MessagePack.</span><span class="sxs-lookup"><span data-stu-id="49af6-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="49af6-266">include il supporto incorporato per il formato MessagePack e fornisce API per il client e il server da utilizzare.</span><span class="sxs-lookup"><span data-stu-id="49af6-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="49af6-267">Configurare MessagePack sul server</span><span class="sxs-lookup"><span data-stu-id="49af6-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="49af6-268">Per abilitare il protocollo Hub MessagePack `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` nel server, installa il pacchetto nell'app.</span><span class="sxs-lookup"><span data-stu-id="49af6-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="49af6-269">Nel `Startup.ConfigureServices` metodo aggiungere `AddMessagePackProtocol` alla `AddSignalR` chiamata per abilitare il supporto MessagePack sul server.</span><span class="sxs-lookup"><span data-stu-id="49af6-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="49af6-270">JSON è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="49af6-270">JSON is enabled by default.</span></span> <span data-ttu-id="49af6-271">L'aggiunta di MessagePack consente il supporto per i client JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="49af6-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="49af6-272">Per personalizzare il modo in `AddMessagePackProtocol` cui MessagePack formatterà i dati, accetta un delegato per la configurazione delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="49af6-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="49af6-273">In tale delegato, la proprietà può essere utilizzata per configurare le opzioni di serializzazione MessagePack.In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span><span class="sxs-lookup"><span data-stu-id="49af6-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="49af6-274">Per ulteriori informazioni sul funzionamento dei sistemi di risoluzione, visitare la libreria MessagePack in [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="49af6-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="49af6-275">Gli attributi possono essere utilizzati sugli oggetti che si desidera serializzare per definire la modalità di gestione da parte degli attributi.</span><span class="sxs-lookup"><span data-stu-id="49af6-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="49af6-276">Si consiglia vivamente di esaminare [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e applicare le patch consigliate.</span><span class="sxs-lookup"><span data-stu-id="49af6-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="49af6-277">Ad esempio, `MessagePackSecurity.Active` l'impostazione della proprietà static su `MessagePackSecurity.UntrustedData`.</span><span class="sxs-lookup"><span data-stu-id="49af6-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="49af6-278">L'impostazione di richiede l'installazione `MessagePackSecurity.Active` manuale di una versione [1.9.x di MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="49af6-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="49af6-279">L'installazione di `MessagePack` 1.9.x SignalR aggiorna la versione.</span><span class="sxs-lookup"><span data-stu-id="49af6-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="49af6-280">Quando `MessagePackSecurity.Active` non è `MessagePackSecurity.UntrustedData`impostato su , un client dannoso potrebbe causare un Denial of Service.</span><span class="sxs-lookup"><span data-stu-id="49af6-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="49af6-281">Impostato `MessagePackSecurity.Active` `Program.Main`in , come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="49af6-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="49af6-282">Configurare MessagePack sul client</span><span class="sxs-lookup"><span data-stu-id="49af6-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="49af6-283">JSON è abilitato per impostazione predefinita per i client supportati.</span><span class="sxs-lookup"><span data-stu-id="49af6-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="49af6-284">I client possono supportare un solo protocollo.</span><span class="sxs-lookup"><span data-stu-id="49af6-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="49af6-285">L'aggiunta del supporto MessagePack sostituirà tutti i protocolli configurati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="49af6-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="49af6-286">Client .NET</span><span class="sxs-lookup"><span data-stu-id="49af6-286">.NET client</span></span>

<span data-ttu-id="49af6-287">Per abilitare MessagePack nel client `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`installare il pacchetto e chiamare su .</span><span class="sxs-lookup"><span data-stu-id="49af6-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="49af6-288">Questa `AddMessagePackProtocol` chiamata accetta un delegato per la configurazione delle opzioni proprio come il server.</span><span class="sxs-lookup"><span data-stu-id="49af6-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="49af6-289">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="49af6-289">JavaScript client</span></span>

<span data-ttu-id="49af6-290">Il supporto MessagePack per il client [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) JavaScript viene fornito dal pacchetto npm.</span><span class="sxs-lookup"><span data-stu-id="49af6-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="49af6-291">Installare il pacchetto eseguendo il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="49af6-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="49af6-292">Dopo aver installato il pacchetto npm, il modulo può essere utilizzato direttamente tramite un caricatore di moduli JavaScript o importato nel browser facendo riferimento al seguente file:</span><span class="sxs-lookup"><span data-stu-id="49af6-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="49af6-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="49af6-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="49af6-294">In un browser, è necessario fare riferimento anche alla `msgpack5` libreria.</span><span class="sxs-lookup"><span data-stu-id="49af6-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="49af6-295">Utilizzare `<script>` un tag per creare un riferimento.</span><span class="sxs-lookup"><span data-stu-id="49af6-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="49af6-296">La libreria è reperibile in *node_modules.*</span><span class="sxs-lookup"><span data-stu-id="49af6-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="49af6-297">Quando si `<script>` utilizza l'elemento, l'ordine è importante.</span><span class="sxs-lookup"><span data-stu-id="49af6-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="49af6-298">Se si fa riferimento a *signalr-protocol-msgpack.js* prima di *msgpack5.js*, si verifica un errore quando si tenta di connettersi con MessagePack.</span><span class="sxs-lookup"><span data-stu-id="49af6-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="49af6-299">*signalr.js* è necessario anche prima *di signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="49af6-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="49af6-300">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` L'aggiunta `HubConnectionBuilder` al client configurerà il client per l'utilizzo del protocollo MessagePack per la connessione a un server.</span><span class="sxs-lookup"><span data-stu-id="49af6-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="49af6-301">Al momento, non sono disponibili opzioni di configurazione per il protocollo MessagePack sul client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="49af6-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="49af6-302">Stranezze di MessagePack</span><span class="sxs-lookup"><span data-stu-id="49af6-302">MessagePack quirks</span></span>

<span data-ttu-id="49af6-303">Quando si utilizza il protocollo Hub MessagePack, è necessario tenere presente alcuni problemi.</span><span class="sxs-lookup"><span data-stu-id="49af6-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="49af6-304">MessagePack fa distinzione tra maiuscole e minuscole</span><span class="sxs-lookup"><span data-stu-id="49af6-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="49af6-305">Il protocollo MessagePack fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="49af6-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="49af6-306">Si consideri, ad esempio, la seguente classe di C:</span><span class="sxs-lookup"><span data-stu-id="49af6-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="49af6-307">Quando si invia dal client JavaScript, è necessario utilizzare `PascalCased` i nomi delle proprietà, poiché l'uso di maiuscole e minuscole deve corrispondere esattamente alla classe di C.</span><span class="sxs-lookup"><span data-stu-id="49af6-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="49af6-308">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="49af6-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="49af6-309">L'utilizzo di `camelCased` nomi non verrà associato correttamente alla classe C.</span><span class="sxs-lookup"><span data-stu-id="49af6-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="49af6-310">È possibile risolvere questo `Key` problema utilizzando l'attributo per specificare un nome diverso per il MessagePack proprietà.</span><span class="sxs-lookup"><span data-stu-id="49af6-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="49af6-311">Per ulteriori informazioni, vedere la documentazione di [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="49af6-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="49af6-312">DateTime.Kind non viene mantenuto durante la serializzazione/deserializzazione</span><span class="sxs-lookup"><span data-stu-id="49af6-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="49af6-313">Il protocollo MessagePack non fornisce un modo `Kind` per `DateTime`codificare il valore di un oggetto .</span><span class="sxs-lookup"><span data-stu-id="49af6-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="49af6-314">Di conseguenza, durante la deserializzazione di una data, il protocollo Hub MessagePack presuppone che la data in ingresso sia in formato UTC.</span><span class="sxs-lookup"><span data-stu-id="49af6-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="49af6-315">Se si utilizzano `DateTime` valori nell'ora locale, è consigliabile eseguire la conversione in UTC prima di inviarli.</span><span class="sxs-lookup"><span data-stu-id="49af6-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="49af6-316">Convertirli dall'ora UTC all'ora locale quando li ricevi.</span><span class="sxs-lookup"><span data-stu-id="49af6-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="49af6-317">Per ulteriori informazioni su questa limitazione, vedere Problema di GitHub [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="49af6-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="49af6-318">DateTime.MinValue non è supportato da MessagePack in JavaScript</span><span class="sxs-lookup"><span data-stu-id="49af6-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="49af6-319">La libreria [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilizzata dal client JavaScript `timestamp96` non supporta il tipo in MessagePack.</span><span class="sxs-lookup"><span data-stu-id="49af6-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="49af6-320">Questo tipo viene utilizzato per codificare valori di data molto grandi (sia molto presto nel passato o molto lontano in futuro).</span><span class="sxs-lookup"><span data-stu-id="49af6-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="49af6-321">Il valore `DateTime.MinValue` `January 1, 0001` di è che `timestamp96` deve essere codificato in un valore.</span><span class="sxs-lookup"><span data-stu-id="49af6-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="49af6-322">Per questo motivo, l'invio `DateTime.MinValue` a un client JavaScript non è supportato.</span><span class="sxs-lookup"><span data-stu-id="49af6-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="49af6-323">Quando `DateTime.MinValue` viene ricevuto dal client JavaScript, viene generato il seguente errore:</span><span class="sxs-lookup"><span data-stu-id="49af6-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="49af6-324">Di `DateTime.MinValue` solito, viene utilizzato per codificare un "mancante" o `null` un valore.</span><span class="sxs-lookup"><span data-stu-id="49af6-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="49af6-325">Se è necessario codificare tale valore in MessagePack,`DateTime?`utilizzare un valore `bool` nullable `DateTime` ( ) o codificare un valore separato che indica se la data è presente.</span><span class="sxs-lookup"><span data-stu-id="49af6-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="49af6-326">Per ulteriori informazioni su questa limitazione, vedere Problema di GitHub [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="49af6-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="49af6-327">Supporto MessagePack nell'ambiente di compilazione "ahead-of-time"</span><span class="sxs-lookup"><span data-stu-id="49af6-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="49af6-328">La libreria [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizzata dal client e dal server .NET utilizza la generazione di codice per ottimizzare la serializzazione.</span><span class="sxs-lookup"><span data-stu-id="49af6-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="49af6-329">Di conseguenza, non è supportato per impostazione predefinita in ambienti che utilizzano la compilazione "ahead-of-time" (ad esempio Xamarin iOS o Unity).</span><span class="sxs-lookup"><span data-stu-id="49af6-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="49af6-330">È possibile utilizzare MessagePack in questi ambienti "pre-generando" il codice del serializzatore/deserializzatore.</span><span class="sxs-lookup"><span data-stu-id="49af6-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="49af6-331">Per ulteriori informazioni, vedere la documentazione di [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="49af6-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="49af6-332">Dopo aver pregenerato i serializzatori, è possibile registrarli `AddMessagePackProtocol`utilizzando il delegato di configurazione passato a :</span><span class="sxs-lookup"><span data-stu-id="49af6-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="49af6-333">I controlli dei tipi sono più rigorosi in MessagePackType checks are more strict in MessagePack</span><span class="sxs-lookup"><span data-stu-id="49af6-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="49af6-334">Il protocollo JSON Hub eseguirà le conversioni dei tipi durante la deserializzazione.</span><span class="sxs-lookup"><span data-stu-id="49af6-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="49af6-335">Ad esempio, se l'oggetto in ingresso dispone`{ foo: 42 }`di un valore di proprietà che `string`è un numero ( ) ma la proprietà nella classe .NET è di tipo , il valore verrà convertito.</span><span class="sxs-lookup"><span data-stu-id="49af6-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="49af6-336">Tuttavia, MessagePack non esegue questa conversione e genererà un'eccezione che può essere visualizzata nei log sul lato server (e nella console):However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span><span class="sxs-lookup"><span data-stu-id="49af6-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="49af6-337">Per ulteriori informazioni su questa limitazione, vedere Problema Di GitHub [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="49af6-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="49af6-338">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="49af6-338">Related resources</span></span>

* [<span data-ttu-id="49af6-339">Guida introduttiva</span><span class="sxs-lookup"><span data-stu-id="49af6-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="49af6-340">Client .NET</span><span class="sxs-lookup"><span data-stu-id="49af6-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="49af6-341">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="49af6-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
