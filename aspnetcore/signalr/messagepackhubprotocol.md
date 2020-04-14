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
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>Usare il protocollo SignalR hub MessagePack in ASP.NET CoreUse MessagePack Hub Protocol in for ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

In questo articolo si presuppone che il lettore abbia familiarità con gli argomenti trattati in [Introduzione](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Che cos'è MessagePack?

[MessagePack](https://msgpack.org/index.html) è un formato di serializzazione binaria veloce e compatto. È utile quando le prestazioni e la larghezza di banda sono un problema perché crea messaggi più piccoli rispetto a [JSON](https://www.json.org/). I messaggi binari sono illeggibili quando si esaminano le tracce di rete e i registri, a meno che i byte non vengano passati tramite un parser MessagePack. SignalRdispone del supporto incorporato per il formato MessagePack e fornisce API per il client e il server da utilizzare.

## <a name="configure-messagepack-on-the-server"></a>Configurare MessagePack sul server

Per abilitare il protocollo Hub MessagePack `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` nel server, installa il pacchetto nell'app. Nel `Startup.ConfigureServices` metodo aggiungere `AddMessagePackProtocol` alla `AddSignalR` chiamata per abilitare il supporto MessagePack sul server.

> [!NOTE]
> JSON è abilitato per impostazione predefinita. L'aggiunta di MessagePack consente il supporto per i client JSON e MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Per personalizzare il modo in `AddMessagePackProtocol` cui MessagePack formatterà i dati, accetta un delegato per la configurazione delle opzioni. In tale delegato, la proprietà può essere utilizzata per configurare le opzioni di serializzazione MessagePack.In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options. Per ulteriori informazioni sul funzionamento dei sistemi di risoluzione, visitare la libreria MessagePack in [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Gli attributi possono essere utilizzati sugli oggetti che si desidera serializzare per definire la modalità di gestione da parte degli attributi.

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
> Si consiglia vivamente di esaminare [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e applicare le patch consigliate. Ad esempio, `.WithSecurity(MessagePackSecurity.UntrustedData)` chiamando quando `SerializerOptions`si sostituisce il file .

## <a name="configure-messagepack-on-the-client"></a>Configurare MessagePack sul client

> [!NOTE]
> JSON è abilitato per impostazione predefinita per i client supportati. I client possono supportare un solo protocollo. L'aggiunta del supporto MessagePack sostituirà tutti i protocolli configurati in precedenza.

### <a name="net-client"></a>Client .NET

Per abilitare MessagePack nel client `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`installare il pacchetto e chiamare su .

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Questa `AddMessagePackProtocol` chiamata accetta un delegato per la configurazione delle opzioni proprio come il server.

### <a name="javascript-client"></a>Client JavaScript

Il supporto MessagePack per il client [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) JavaScript viene fornito dal pacchetto npm. Installare il pacchetto eseguendo il comando seguente in una shell dei comandi:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Dopo aver installato il pacchetto npm, il modulo può essere utilizzato direttamente tramite un caricatore di moduli JavaScript o importato nel browser facendo riferimento al seguente file:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

In un browser, è necessario fare riferimento anche alla `msgpack5` libreria. Utilizzare `<script>` un tag per creare un riferimento. La libreria è reperibile in *node_modules.*

> [!NOTE]
> Quando si `<script>` utilizza l'elemento, l'ordine è importante. Se si fa riferimento a *signalr-protocol-msgpack.js* prima di *msgpack5.js*, si verifica un errore quando si tenta di connettersi con MessagePack. *signalr.js* è necessario anche prima *di signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` L'aggiunta `HubConnectionBuilder` al client configurerà il client per l'utilizzo del protocollo MessagePack per la connessione a un server.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Al momento, non sono disponibili opzioni di configurazione per il protocollo MessagePack sul client JavaScript.

## <a name="messagepack-quirks"></a>Stranezze di MessagePack

Quando si utilizza il protocollo Hub MessagePack, è necessario tenere presente alcuni problemi.

### <a name="messagepack-is-case-sensitive"></a>MessagePack fa distinzione tra maiuscole e minuscole

Il protocollo MessagePack fa distinzione tra maiuscole e minuscole. Si consideri, ad esempio, la seguente classe di C:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Quando si invia dal client JavaScript, è necessario utilizzare `PascalCased` i nomi delle proprietà, poiché l'uso di maiuscole e minuscole deve corrispondere esattamente alla classe di C. Ad esempio:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

L'utilizzo di `camelCased` nomi non verrà associato correttamente alla classe C. È possibile risolvere questo `Key` problema utilizzando l'attributo per specificare un nome diverso per il MessagePack proprietà. Per ulteriori informazioni, vedere la documentazione di [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind non viene mantenuto durante la serializzazione/deserializzazione

Il protocollo MessagePack non fornisce un modo `Kind` per `DateTime`codificare il valore di un oggetto . Di conseguenza, quando si deserializza una data, il protocollo `DateTime.Kind` Hub `DateTimeKind.Local` MessagePack verrà convertito nel formato UTC se il è in caso contrario non toccherà l'ora e lo passerà così com'è. Se si utilizzano `DateTime` valori, è consigliabile eseguire la conversione in UTC prima di inviarli. Convertirli dall'ora UTC all'ora locale quando li ricevi.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue non è supportato da MessagePack in JavaScript

La libreria [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilizzata dal client JavaScript `timestamp96` non supporta il tipo in MessagePack. Questo tipo viene utilizzato per codificare valori di data molto grandi (sia molto presto nel passato o molto lontano in futuro). Il valore `DateTime.MinValue` `January 1, 0001`di è , che `timestamp96` deve essere codificato in un valore. Per questo motivo, l'invio `DateTime.MinValue` a un client JavaScript non è supportato. Quando `DateTime.MinValue` viene ricevuto dal client JavaScript, viene generato il seguente errore:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Di `DateTime.MinValue` solito, viene utilizzato per codificare un "mancante" o `null` un valore. Se è necessario codificare tale valore in MessagePack,`DateTime?`utilizzare un valore `bool` nullable `DateTime` ( ) o codificare un valore separato che indica se la data è presente.

Per ulteriori informazioni su questa limitazione, vedere Problema di GitHub [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Supporto MessagePack nell'ambiente di compilazione "ahead-of-time"

La libreria [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilizzata dal client e dal server .NET utilizza la generazione di codice per ottimizzare la serializzazione. Di conseguenza, non è supportato per impostazione predefinita in ambienti che utilizzano la compilazione "ahead-of-time" (ad esempio Xamarin iOS o Unity). È possibile utilizzare MessagePack in questi ambienti "pre-generando" il codice del serializzatore/deserializzatore. Per ulteriori informazioni, vedere la documentazione di [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Dopo aver pregenerato i serializzatori, è possibile registrarli `AddMessagePackProtocol`utilizzando il delegato di configurazione passato a :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>I controlli dei tipi sono più rigorosi in MessagePackType checks are more strict in MessagePack

Il protocollo JSON Hub eseguirà le conversioni dei tipi durante la deserializzazione. Ad esempio, se l'oggetto in ingresso dispone`{ foo: 42 }`di un valore di proprietà che `string`è un numero ( ) ma la proprietà nella classe .NET è di tipo , il valore verrà convertito. Tuttavia, MessagePack non esegue questa conversione e genererà un'eccezione che può essere visualizzata nei log sul lato server (e nella console):However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Per ulteriori informazioni su questa limitazione, vedere Problema Di GitHub [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Risorse correlate

* [Guida introduttiva](xref:tutorials/signalr)
* [Client .NET](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

In questo articolo si presuppone che il lettore abbia familiarità con gli argomenti trattati in [Introduzione](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Che cos'è MessagePack?

[MessagePack](https://msgpack.org/index.html) è un formato di serializzazione binaria veloce e compatto. È utile quando le prestazioni e la larghezza di banda sono un problema perché crea messaggi più piccoli rispetto a [JSON](https://www.json.org/). I messaggi binari sono illeggibili quando si esaminano le tracce di rete e i registri, a meno che i byte non vengano passati tramite un parser MessagePack. SignalRinclude il supporto incorporato per il formato MessagePack e fornisce API per il client e il server da utilizzare.

## <a name="configure-messagepack-on-the-server"></a>Configurare MessagePack sul server

Per abilitare il protocollo Hub MessagePack `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` nel server, installa il pacchetto nell'app. Nel `Startup.ConfigureServices` metodo aggiungere `AddMessagePackProtocol` alla `AddSignalR` chiamata per abilitare il supporto MessagePack sul server.

> [!NOTE]
> JSON è abilitato per impostazione predefinita. L'aggiunta di MessagePack consente il supporto per i client JSON e MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Per personalizzare il modo in `AddMessagePackProtocol` cui MessagePack formatterà i dati, accetta un delegato per la configurazione delle opzioni. In tale delegato, la proprietà può essere utilizzata per configurare le opzioni di serializzazione MessagePack.In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options. Per ulteriori informazioni sul funzionamento dei sistemi di risoluzione, visitare la libreria MessagePack in [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Gli attributi possono essere utilizzati sugli oggetti che si desidera serializzare per definire la modalità di gestione da parte degli attributi.

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
> Si consiglia vivamente di esaminare [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e applicare le patch consigliate. Ad esempio, `MessagePackSecurity.Active` l'impostazione della proprietà static su `MessagePackSecurity.UntrustedData`. L'impostazione di richiede l'installazione `MessagePackSecurity.Active` manuale di una versione [1.9.x di MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). L'installazione di `MessagePack` 1.9.x SignalR aggiorna la versione. Quando `MessagePackSecurity.Active` non è `MessagePackSecurity.UntrustedData`impostato su , un client dannoso potrebbe causare un Denial of Service. Impostato `MessagePackSecurity.Active` `Program.Main`in , come illustrato nel codice seguente:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurare MessagePack sul client

> [!NOTE]
> JSON è abilitato per impostazione predefinita per i client supportati. I client possono supportare un solo protocollo. L'aggiunta del supporto MessagePack sostituirà tutti i protocolli configurati in precedenza.

### <a name="net-client"></a>Client .NET

Per abilitare MessagePack nel client `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`installare il pacchetto e chiamare su .

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Questa `AddMessagePackProtocol` chiamata accetta un delegato per la configurazione delle opzioni proprio come il server.

### <a name="javascript-client"></a>Client JavaScript

Il supporto MessagePack per il client [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) JavaScript viene fornito dal pacchetto npm. Installare il pacchetto eseguendo il comando seguente in una shell dei comandi:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Dopo aver installato il pacchetto npm, il modulo può essere utilizzato direttamente tramite un caricatore di moduli JavaScript o importato nel browser facendo riferimento al seguente file:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

In un browser, è necessario fare riferimento anche alla `msgpack5` libreria. Utilizzare `<script>` un tag per creare un riferimento. La libreria è reperibile in *node_modules.*

> [!NOTE]
> Quando si `<script>` utilizza l'elemento, l'ordine è importante. Se si fa riferimento a *signalr-protocol-msgpack.js* prima di *msgpack5.js*, si verifica un errore quando si tenta di connettersi con MessagePack. *signalr.js* è necessario anche prima *di signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` L'aggiunta `HubConnectionBuilder` al client configurerà il client per l'utilizzo del protocollo MessagePack per la connessione a un server.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Al momento, non sono disponibili opzioni di configurazione per il protocollo MessagePack sul client JavaScript.

## <a name="messagepack-quirks"></a>Stranezze di MessagePack

Quando si utilizza il protocollo Hub MessagePack, è necessario tenere presente alcuni problemi.

### <a name="messagepack-is-case-sensitive"></a>MessagePack fa distinzione tra maiuscole e minuscole

Il protocollo MessagePack fa distinzione tra maiuscole e minuscole. Si consideri, ad esempio, la seguente classe di C:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Quando si invia dal client JavaScript, è necessario utilizzare `PascalCased` i nomi delle proprietà, poiché l'uso di maiuscole e minuscole deve corrispondere esattamente alla classe di C. Ad esempio:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

L'utilizzo di `camelCased` nomi non verrà associato correttamente alla classe C. È possibile risolvere questo `Key` problema utilizzando l'attributo per specificare un nome diverso per il MessagePack proprietà. Per ulteriori informazioni, vedere la documentazione di [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind non viene mantenuto durante la serializzazione/deserializzazione

Il protocollo MessagePack non fornisce un modo `Kind` per `DateTime`codificare il valore di un oggetto . Di conseguenza, durante la deserializzazione di una data, il protocollo Hub MessagePack presuppone che la data in ingresso sia in formato UTC. Se si utilizzano `DateTime` valori nell'ora locale, è consigliabile eseguire la conversione in UTC prima di inviarli. Convertirli dall'ora UTC all'ora locale quando li ricevi.

Per ulteriori informazioni su questa limitazione, vedere Problema di GitHub [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue non è supportato da MessagePack in JavaScript

La libreria [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilizzata dal client JavaScript `timestamp96` non supporta il tipo in MessagePack. Questo tipo viene utilizzato per codificare valori di data molto grandi (sia molto presto nel passato o molto lontano in futuro). Il valore `DateTime.MinValue` `January 1, 0001`di è , che `timestamp96` deve essere codificato in un valore. Per questo motivo, l'invio `DateTime.MinValue` a un client JavaScript non è supportato. Quando `DateTime.MinValue` viene ricevuto dal client JavaScript, viene generato il seguente errore:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Di `DateTime.MinValue` solito, viene utilizzato per codificare un "mancante" o `null` un valore. Se è necessario codificare tale valore in MessagePack,`DateTime?`utilizzare un valore `bool` nullable `DateTime` ( ) o codificare un valore separato che indica se la data è presente.

Per ulteriori informazioni su questa limitazione, vedere Problema di GitHub [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Supporto MessagePack nell'ambiente di compilazione "ahead-of-time"

La libreria [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizzata dal client e dal server .NET utilizza la generazione di codice per ottimizzare la serializzazione. Di conseguenza, non è supportato per impostazione predefinita in ambienti che utilizzano la compilazione "ahead-of-time" (ad esempio Xamarin iOS o Unity). È possibile utilizzare MessagePack in questi ambienti "pre-generando" il codice del serializzatore/deserializzatore. Per ulteriori informazioni, vedere la documentazione di [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Dopo aver pregenerato i serializzatori, è possibile registrarli `AddMessagePackProtocol`utilizzando il delegato di configurazione passato a :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>I controlli dei tipi sono più rigorosi in MessagePackType checks are more strict in MessagePack

Il protocollo JSON Hub eseguirà le conversioni dei tipi durante la deserializzazione. Ad esempio, se l'oggetto in ingresso dispone`{ foo: 42 }`di un valore di proprietà che `string`è un numero ( ) ma la proprietà nella classe .NET è di tipo , il valore verrà convertito. Tuttavia, MessagePack non esegue questa conversione e genererà un'eccezione che può essere visualizzata nei log sul lato server (e nella console):However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Per ulteriori informazioni su questa limitazione, vedere Problema Di GitHub [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Risorse correlate

* [Guida introduttiva](xref:tutorials/signalr)
* [Client .NET](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In questo articolo si presuppone che il lettore abbia familiarità con gli argomenti trattati in [Introduzione](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Che cos'è MessagePack?

[MessagePack](https://msgpack.org/index.html) è un formato di serializzazione binaria veloce e compatto. È utile quando le prestazioni e la larghezza di banda sono un problema perché crea messaggi più piccoli rispetto a [JSON](https://www.json.org/). I messaggi binari sono illeggibili quando si esaminano le tracce di rete e i registri, a meno che i byte non vengano passati tramite un parser MessagePack. SignalRinclude il supporto incorporato per il formato MessagePack e fornisce API per il client e il server da utilizzare.

## <a name="configure-messagepack-on-the-server"></a>Configurare MessagePack sul server

Per abilitare il protocollo Hub MessagePack `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` nel server, installa il pacchetto nell'app. Nel `Startup.ConfigureServices` metodo aggiungere `AddMessagePackProtocol` alla `AddSignalR` chiamata per abilitare il supporto MessagePack sul server.

> [!NOTE]
> JSON è abilitato per impostazione predefinita. L'aggiunta di MessagePack consente il supporto per i client JSON e MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Per personalizzare il modo in `AddMessagePackProtocol` cui MessagePack formatterà i dati, accetta un delegato per la configurazione delle opzioni. In tale delegato, la proprietà può essere utilizzata per configurare le opzioni di serializzazione MessagePack.In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options. Per ulteriori informazioni sul funzionamento dei sistemi di risoluzione, visitare la libreria MessagePack in [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Gli attributi possono essere utilizzati sugli oggetti che si desidera serializzare per definire la modalità di gestione da parte degli attributi.

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
> Si consiglia vivamente di esaminare [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e applicare le patch consigliate. Ad esempio, `MessagePackSecurity.Active` l'impostazione della proprietà static su `MessagePackSecurity.UntrustedData`. L'impostazione di richiede l'installazione `MessagePackSecurity.Active` manuale di una versione [1.9.x di MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). L'installazione di `MessagePack` 1.9.x SignalR aggiorna la versione. Quando `MessagePackSecurity.Active` non è `MessagePackSecurity.UntrustedData`impostato su , un client dannoso potrebbe causare un Denial of Service. Impostato `MessagePackSecurity.Active` `Program.Main`in , come illustrato nel codice seguente:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurare MessagePack sul client

> [!NOTE]
> JSON è abilitato per impostazione predefinita per i client supportati. I client possono supportare un solo protocollo. L'aggiunta del supporto MessagePack sostituirà tutti i protocolli configurati in precedenza.

### <a name="net-client"></a>Client .NET

Per abilitare MessagePack nel client `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`installare il pacchetto e chiamare su .

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Questa `AddMessagePackProtocol` chiamata accetta un delegato per la configurazione delle opzioni proprio come il server.

### <a name="javascript-client"></a>Client JavaScript

Il supporto MessagePack per il client [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) JavaScript viene fornito dal pacchetto npm. Installare il pacchetto eseguendo il comando seguente in una shell dei comandi:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Dopo aver installato il pacchetto npm, il modulo può essere utilizzato direttamente tramite un caricatore di moduli JavaScript o importato nel browser facendo riferimento al seguente file:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

In un browser, è necessario fare riferimento anche alla `msgpack5` libreria. Utilizzare `<script>` un tag per creare un riferimento. La libreria è reperibile in *node_modules.*

> [!NOTE]
> Quando si `<script>` utilizza l'elemento, l'ordine è importante. Se si fa riferimento a *signalr-protocol-msgpack.js* prima di *msgpack5.js*, si verifica un errore quando si tenta di connettersi con MessagePack. *signalr.js* è necessario anche prima *di signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` L'aggiunta `HubConnectionBuilder` al client configurerà il client per l'utilizzo del protocollo MessagePack per la connessione a un server.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Al momento, non sono disponibili opzioni di configurazione per il protocollo MessagePack sul client JavaScript.

## <a name="messagepack-quirks"></a>Stranezze di MessagePack

Quando si utilizza il protocollo Hub MessagePack, è necessario tenere presente alcuni problemi.

### <a name="messagepack-is-case-sensitive"></a>MessagePack fa distinzione tra maiuscole e minuscole

Il protocollo MessagePack fa distinzione tra maiuscole e minuscole. Si consideri, ad esempio, la seguente classe di C:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Quando si invia dal client JavaScript, è necessario utilizzare `PascalCased` i nomi delle proprietà, poiché l'uso di maiuscole e minuscole deve corrispondere esattamente alla classe di C. Ad esempio:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

L'utilizzo di `camelCased` nomi non verrà associato correttamente alla classe C. È possibile risolvere questo `Key` problema utilizzando l'attributo per specificare un nome diverso per il MessagePack proprietà. Per ulteriori informazioni, vedere la documentazione di [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind non viene mantenuto durante la serializzazione/deserializzazione

Il protocollo MessagePack non fornisce un modo `Kind` per `DateTime`codificare il valore di un oggetto . Di conseguenza, durante la deserializzazione di una data, il protocollo Hub MessagePack presuppone che la data in ingresso sia in formato UTC. Se si utilizzano `DateTime` valori nell'ora locale, è consigliabile eseguire la conversione in UTC prima di inviarli. Convertirli dall'ora UTC all'ora locale quando li ricevi.

Per ulteriori informazioni su questa limitazione, vedere Problema di GitHub [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue non è supportato da MessagePack in JavaScript

La libreria [msgpack5](https://github.com/mcollina/msgpack5) SignalR utilizzata dal client JavaScript `timestamp96` non supporta il tipo in MessagePack. Questo tipo viene utilizzato per codificare valori di data molto grandi (sia molto presto nel passato o molto lontano in futuro). Il valore `DateTime.MinValue` `January 1, 0001` di è che `timestamp96` deve essere codificato in un valore. Per questo motivo, l'invio `DateTime.MinValue` a un client JavaScript non è supportato. Quando `DateTime.MinValue` viene ricevuto dal client JavaScript, viene generato il seguente errore:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Di `DateTime.MinValue` solito, viene utilizzato per codificare un "mancante" o `null` un valore. Se è necessario codificare tale valore in MessagePack,`DateTime?`utilizzare un valore `bool` nullable `DateTime` ( ) o codificare un valore separato che indica se la data è presente.

Per ulteriori informazioni su questa limitazione, vedere Problema di GitHub [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Supporto MessagePack nell'ambiente di compilazione "ahead-of-time"

La libreria [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizzata dal client e dal server .NET utilizza la generazione di codice per ottimizzare la serializzazione. Di conseguenza, non è supportato per impostazione predefinita in ambienti che utilizzano la compilazione "ahead-of-time" (ad esempio Xamarin iOS o Unity). È possibile utilizzare MessagePack in questi ambienti "pre-generando" il codice del serializzatore/deserializzatore. Per ulteriori informazioni, vedere la documentazione di [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Dopo aver pregenerato i serializzatori, è possibile registrarli `AddMessagePackProtocol`utilizzando il delegato di configurazione passato a :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>I controlli dei tipi sono più rigorosi in MessagePackType checks are more strict in MessagePack

Il protocollo JSON Hub eseguirà le conversioni dei tipi durante la deserializzazione. Ad esempio, se l'oggetto in ingresso dispone`{ foo: 42 }`di un valore di proprietà che `string`è un numero ( ) ma la proprietà nella classe .NET è di tipo , il valore verrà convertito. Tuttavia, MessagePack non esegue questa conversione e genererà un'eccezione che può essere visualizzata nei log sul lato server (e nella console):However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Per ulteriori informazioni su questa limitazione, vedere Problema Di GitHub [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Risorse correlate

* [Guida introduttiva](xref:tutorials/signalr)
* [Client .NET](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)

::: moniker-end
