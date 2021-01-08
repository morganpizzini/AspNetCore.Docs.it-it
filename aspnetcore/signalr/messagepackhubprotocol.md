---
title: Usare il protocollo dell'hub MessagePack in SignalR per ASP.NET Core
author: bradygaster
description: Aggiungere il protocollo dell'hub MessagePack a ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
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
ms.openlocfilehash: 7c3640e9cd2c5d392400a115813584861f789554
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024691"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a>Usare il protocollo dell'hub MessagePack in SignalR per ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

Questo articolo presuppone che il lettore abbia familiarità con gli argomenti trattati in [Introduzione](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Che cos'è MessagePack?

[MessagePack](https://msgpack.org/index.html) è un formato di serializzazione binaria veloce e compatto. È utile quando le prestazioni e la larghezza di banda rappresentano un problema perché crea messaggi più piccoli rispetto a [JSON](https://www.json.org/). I messaggi binari sono illeggibili quando si esaminano le tracce di rete e i log, a meno che i byte non vengano passati tramite un parser MessagePack. SignalR dispone del supporto incorporato per il formato MessagePack e fornisce API per il client e il server da utilizzare.

## <a name="configure-messagepack-on-the-server"></a>Configurare MessagePack nel server

Per abilitare il protocollo dell'hub MessagePack nel server, installare il `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacchetto nell'app. Nel `Startup.ConfigureServices` metodo aggiungere `AddMessagePackProtocol` alla `AddSignalR` chiamata per abilitare il supporto di MessagePack nel server.

> [!NOTE]
> JSON è abilitato per impostazione predefinita. L'aggiunta di MessagePack Abilita il supporto per i client JSON e MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Per personalizzare il modo in cui MessagePack formatterà i dati, `AddMessagePackProtocol` accetta un delegato per la configurazione delle opzioni. In tale delegato, `SerializerOptions` è possibile usare la proprietà per configurare le opzioni di serializzazione MessagePack. Per ulteriori informazioni sul funzionamento dei resolver, visitare la libreria MessagePack in [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Gli attributi possono essere utilizzati per gli oggetti che si desidera serializzare per definire il modo in cui devono essere gestiti.

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
> Si consiglia vivamente di esaminare [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e di applicare le patch consigliate. Ad esempio, chiamando `.WithSecurity(MessagePackSecurity.UntrustedData)` quando si sostituisce `SerializerOptions` .

## <a name="configure-messagepack-on-the-client"></a>Configurare MessagePack nel client

> [!NOTE]
> JSON è abilitato per impostazione predefinita per i client supportati. I client possono supportare un solo protocollo. L'aggiunta del supporto di MessagePack sostituirà tutti i protocolli configurati in precedenza.

### <a name="net-client"></a>Client .NET

Per abilitare MessagePack nel client .NET, installare il `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacchetto e chiamare il `AddMessagePackProtocol` `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Questa `AddMessagePackProtocol` chiamata accetta un delegato per la configurazione delle opzioni esattamente come il server.

### <a name="javascript-client"></a>Client JavaScript

Il supporto di MessagePack per il client JavaScript viene fornito dal [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pacchetto NPM. Installare il pacchetto eseguendo il comando seguente in una shell dei comandi:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Dopo l'installazione del pacchetto NPM, il modulo può essere usato direttamente tramite un caricatore di moduli JavaScript o importato nel browser facendo riferimento al file seguente:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

In un browser `msgpack5` è necessario fare riferimento anche alla raccolta. Usare un `<script>` tag per creare un riferimento. La libreria si trova in *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Quando si usa l' `<script>` elemento, l'ordine è importante. Se *signalr-protocol-msgpack.js* viene fatto riferimento prima *msgpack5.js*, si verifica un errore durante il tentativo di connessione a MessagePack. *signalr.js* è necessario anche prima di *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` Se si aggiunge a, il client viene configurato per l'utilizzo del protocollo MessagePack durante la connessione a un server.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Al momento non sono disponibili opzioni di configurazione per il protocollo MessagePack nel client JavaScript.

### <a name="java-client"></a>Client Java

Per abilitare MessagePack con Java, installare il `com.microsoft.signalr.messagepack` pacchetto. Quando si usa Gradle, aggiungere la riga seguente alla `dependencies` sezione del file *Build. Gradle* :

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

Quando si usa Maven, aggiungere le righe seguenti all'interno dell' `<dependencies>` elemento del file di *pom.xml* :

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

Chiamare `withHubProtocol(new MessagePackHubProtocol())` il `HubConnectionBuilder` .

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a>Peculiarità di MessagePack

Quando si usa il protocollo dell'hub MessagePack, è necessario tenere presenti alcuni aspetti.

### <a name="messagepack-is-case-sensitive"></a>MessagePack fa distinzione tra maiuscole e minuscole

Il protocollo MessagePack fa distinzione tra maiuscole e minuscole. Si consideri, ad esempio, la classe C# seguente:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Quando si invia dal client JavaScript, è necessario usare i `PascalCased` nomi delle proprietà, perché la combinazione di maiuscole e minuscole deve corrispondere esattamente alla classe C#. Ad esempio:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

`camelCased`L'uso di nomi non verrà associato correttamente alla classe C#. Per aggirare questo problema, è possibile usare l' `Key` attributo per specificare un nome diverso per la proprietà MessagePack. Per ulteriori informazioni, vedere [la documentazione MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind non viene mantenuto durante la serializzazione/deserializzazione

Il protocollo MessagePack non fornisce un modo per codificare il `Kind` valore di un oggetto `DateTime` . Di conseguenza, quando si deserializza una data, il protocollo dell'hub MessagePack viene convertito nel formato UTC se `DateTime.Kind` è `DateTimeKind.Local` altrimenti non toccherà il tempo e lo passerà così com'è. Se si usano `DateTime` i valori, è consigliabile eseguire la conversione in formato UTC prima di inviarli. Convertirli dall'ora UTC all'ora locale quando vengono ricevuti.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue non è supportato da MessagePack in JavaScript

La libreria [msgpack5](https://github.com/mcollina/msgpack5) usata dal SignalR client JavaScript non supporta il `timestamp96` tipo in MessagePack. Questo tipo viene usato per codificare i valori di data molto grandi (molto presto in passato o molto lontano in futuro). Il valore di `DateTime.MinValue` è `January 1, 0001` , che deve essere codificato in un `timestamp96` valore. Per questo motivo, l'invio `DateTime.MinValue` a un client JavaScript non è supportato. Quando `DateTime.MinValue` viene ricevuto dal client JavaScript, viene generato l'errore seguente:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

In genere, `DateTime.MinValue` viene usato per codificare un valore "Missing" o `null` . Se è necessario codificare tale valore in MessagePack, utilizzare un `DateTime` valore Nullable ( `DateTime?` ) o codificare un valore distinto che `bool` indica se la data è presente.

Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Supporto di MessagePack nell'ambiente di compilazione "Ahead of Time"

La libreria [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilizzata dal client e dal server .NET utilizza la generazione del codice per ottimizzare la serializzazione. Di conseguenza, non è supportato per impostazione predefinita negli ambienti che usano la compilazione "Ahead of Time" (ad esempio, Novell iOS o Unity). È possibile usare MessagePack in questi ambienti mediante la "pre-generazione" del codice del serializzatore/deserializzatore. Per ulteriori informazioni, vedere [la documentazione MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Una volta creati in precedenza i serializzatori, è possibile registrarli usando il delegato di configurazione passato a `AddMessagePackProtocol` :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>I controlli del tipo sono più severi in MessagePack

Il protocollo dell'hub JSON eseguirà le conversioni dei tipi durante la deserializzazione. Se, ad esempio, l'oggetto in ingresso ha un valore della proprietà che è un numero ( `{ foo: 42 }` ), ma la proprietà nella classe .NET è di tipo `string` , il valore verrà convertito. Tuttavia, MessagePack non esegue questa conversione e genererà un'eccezione che può essere visualizzata nei log lato server (e nella console):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

### <a name="chars-and-strings-in-java"></a>Caratteri e stringhe in Java

Nel client Java gli `char` oggetti verranno serializzati come oggetti di un solo carattere `String` . Questo si differenzia dal client C# e JavaScript, che li serializza come `short` oggetti. La specifica MessagePack non definisce il comportamento degli `char` oggetti, pertanto spetta all'autore della libreria determinare come serializzarli. La differenza nel comportamento tra i client è il risultato delle librerie usate per le implementazioni.

## <a name="related-resources"></a>Risorse correlate

* [Introduzione](xref:tutorials/signalr)
* [Client .NET](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Questo articolo presuppone che il lettore abbia familiarità con gli argomenti trattati in [Introduzione](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Che cos'è MessagePack?

[MessagePack](https://msgpack.org/index.html) è un formato di serializzazione binaria veloce e compatto. È utile quando le prestazioni e la larghezza di banda rappresentano un problema perché crea messaggi più piccoli rispetto a [JSON](https://www.json.org/). I messaggi binari sono illeggibili quando si esaminano le tracce di rete e i log, a meno che i byte non vengano passati tramite un parser MessagePack. SignalR dispone del supporto incorporato per il formato MessagePack e fornisce le API per il client e il server da utilizzare.

## <a name="configure-messagepack-on-the-server"></a>Configurare MessagePack nel server

Per abilitare il protocollo dell'hub MessagePack nel server, installare il `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacchetto nell'app. Nel `Startup.ConfigureServices` metodo aggiungere `AddMessagePackProtocol` alla `AddSignalR` chiamata per abilitare il supporto di MessagePack nel server.

> [!NOTE]
> JSON è abilitato per impostazione predefinita. L'aggiunta di MessagePack Abilita il supporto per i client JSON e MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Per personalizzare il modo in cui MessagePack formatterà i dati, `AddMessagePackProtocol` accetta un delegato per la configurazione delle opzioni. In tale delegato, `FormatterResolvers` è possibile usare la proprietà per configurare le opzioni di serializzazione MessagePack. Per ulteriori informazioni sul funzionamento dei resolver, visitare la libreria MessagePack in [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Gli attributi possono essere utilizzati per gli oggetti che si desidera serializzare per definire il modo in cui devono essere gestiti.

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
> Si consiglia vivamente di esaminare [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e di applicare le patch consigliate. Ad esempio, impostando la `MessagePackSecurity.Active` proprietà statica su `MessagePackSecurity.UntrustedData` . L'impostazione `MessagePackSecurity.Active` di richiede l'installazione manuale [di una versione 1.9. x di MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). `MessagePack`L'installazione di 1.9. x aggiorna la versione SignalR utilizzata da. `MessagePack` la versione 2. x ha introdotto modifiche di rilievo ed è incompatibile con le SignalR versioni 3,1 e precedenti. Quando `MessagePackSecurity.Active` non è impostato su `MessagePackSecurity.UntrustedData` , un client dannoso potrebbe causare un attacco Denial of Service. Impostare `MessagePackSecurity.Active` in `Program.Main` , come illustrato nel codice seguente:

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurare MessagePack nel client

> [!NOTE]
> JSON è abilitato per impostazione predefinita per i client supportati. I client possono supportare un solo protocollo. L'aggiunta del supporto di MessagePack sostituirà tutti i protocolli configurati in precedenza.

### <a name="net-client"></a>Client .NET

Per abilitare MessagePack nel client .NET, installare il `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacchetto e chiamare il `AddMessagePackProtocol` `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Questa `AddMessagePackProtocol` chiamata accetta un delegato per la configurazione delle opzioni esattamente come il server.

### <a name="javascript-client"></a>Client JavaScript

Il supporto di MessagePack per il client JavaScript viene fornito dal [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pacchetto NPM. Installare il pacchetto eseguendo il comando seguente in una shell dei comandi:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Dopo l'installazione del pacchetto NPM, il modulo può essere usato direttamente tramite un caricatore di moduli JavaScript o importato nel browser facendo riferimento al file seguente:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

In un browser `msgpack5` è necessario fare riferimento anche alla raccolta. Usare un `<script>` tag per creare un riferimento. La libreria si trova in *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Quando si usa l' `<script>` elemento, l'ordine è importante. Se *signalr-protocol-msgpack.js* viene fatto riferimento prima *msgpack5.js*, si verifica un errore durante il tentativo di connessione a MessagePack. *signalr.js* è necessario anche prima di *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` Se si aggiunge a, il client viene configurato per l'utilizzo del protocollo MessagePack durante la connessione a un server.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Al momento non sono disponibili opzioni di configurazione per il protocollo MessagePack nel client JavaScript.

## <a name="messagepack-quirks"></a>Peculiarità di MessagePack

Quando si usa il protocollo dell'hub MessagePack, è necessario tenere presenti alcuni aspetti.

### <a name="messagepack-is-case-sensitive"></a>MessagePack fa distinzione tra maiuscole e minuscole

Il protocollo MessagePack fa distinzione tra maiuscole e minuscole. Si consideri, ad esempio, la classe C# seguente:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Quando si invia dal client JavaScript, è necessario usare i `PascalCased` nomi delle proprietà, perché la combinazione di maiuscole e minuscole deve corrispondere esattamente alla classe C#. Ad esempio:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

`camelCased`L'uso di nomi non verrà associato correttamente alla classe C#. Per aggirare questo problema, è possibile usare l' `Key` attributo per specificare un nome diverso per la proprietà MessagePack. Per ulteriori informazioni, vedere [la documentazione MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind non viene mantenuto durante la serializzazione/deserializzazione

Il protocollo MessagePack non fornisce un modo per codificare il `Kind` valore di un oggetto `DateTime` . Di conseguenza, quando si deserializza una data, il protocollo dell'hub MessagePack presuppone che la data in ingresso sia in formato UTC. Se si usano `DateTime` i valori nell'ora locale, è consigliabile eseguire la conversione in formato UTC prima di inviarli. Convertirli dall'ora UTC all'ora locale quando vengono ricevuti.

Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue non è supportato da MessagePack in JavaScript

La libreria [msgpack5](https://github.com/mcollina/msgpack5) usata dal SignalR client JavaScript non supporta il `timestamp96` tipo in MessagePack. Questo tipo viene usato per codificare i valori di data molto grandi (molto presto in passato o molto lontano in futuro). Il valore di `DateTime.MinValue` è `January 1, 0001` , che deve essere codificato in un `timestamp96` valore. Per questo motivo, l'invio `DateTime.MinValue` a un client JavaScript non è supportato. Quando `DateTime.MinValue` viene ricevuto dal client JavaScript, viene generato l'errore seguente:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

In genere, `DateTime.MinValue` viene usato per codificare un valore "Missing" o `null` . Se è necessario codificare tale valore in MessagePack, utilizzare un `DateTime` valore Nullable ( `DateTime?` ) o codificare un valore distinto che `bool` indica se la data è presente.

Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Supporto di MessagePack nell'ambiente di compilazione "Ahead of Time"

La libreria [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizzata dal client e dal server .NET utilizza la generazione del codice per ottimizzare la serializzazione. Di conseguenza, non è supportato per impostazione predefinita negli ambienti che usano la compilazione "Ahead of Time" (ad esempio, Novell iOS o Unity). È possibile usare MessagePack in questi ambienti mediante la "pre-generazione" del codice del serializzatore/deserializzatore. Per ulteriori informazioni, vedere [la documentazione MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Una volta creati in precedenza i serializzatori, è possibile registrarli usando il delegato di configurazione passato a `AddMessagePackProtocol` :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>I controlli del tipo sono più severi in MessagePack

Il protocollo dell'hub JSON eseguirà le conversioni dei tipi durante la deserializzazione. Se, ad esempio, l'oggetto in ingresso ha un valore della proprietà che è un numero ( `{ foo: 42 }` ), ma la proprietà nella classe .NET è di tipo `string` , il valore verrà convertito. Tuttavia, MessagePack non esegue questa conversione e genererà un'eccezione che può essere visualizzata nei log lato server (e nella console):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Risorse correlate

* [Introduzione](xref:tutorials/signalr)
* [Client .NET](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Questo articolo presuppone che il lettore abbia familiarità con gli argomenti trattati in [Introduzione](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Che cos'è MessagePack?

[MessagePack](https://msgpack.org/index.html) è un formato di serializzazione binaria veloce e compatto. È utile quando le prestazioni e la larghezza di banda rappresentano un problema perché crea messaggi più piccoli rispetto a [JSON](https://www.json.org/). I messaggi binari sono illeggibili quando si esaminano le tracce di rete e i log, a meno che i byte non vengano passati tramite un parser MessagePack. SignalR dispone del supporto incorporato per il formato MessagePack e fornisce le API per il client e il server da utilizzare.

## <a name="configure-messagepack-on-the-server"></a>Configurare MessagePack nel server

Per abilitare il protocollo dell'hub MessagePack nel server, installare il `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacchetto nell'app. Nel `Startup.ConfigureServices` metodo aggiungere `AddMessagePackProtocol` alla `AddSignalR` chiamata per abilitare il supporto di MessagePack nel server.

> [!NOTE]
> JSON è abilitato per impostazione predefinita. L'aggiunta di MessagePack Abilita il supporto per i client JSON e MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Per personalizzare il modo in cui MessagePack formatterà i dati, `AddMessagePackProtocol` accetta un delegato per la configurazione delle opzioni. In tale delegato, `FormatterResolvers` è possibile usare la proprietà per configurare le opzioni di serializzazione MessagePack. Per ulteriori informazioni sul funzionamento dei resolver, visitare la libreria MessagePack in [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Gli attributi possono essere utilizzati per gli oggetti che si desidera serializzare per definire il modo in cui devono essere gestiti.

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
> Si consiglia vivamente di esaminare [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e di applicare le patch consigliate. Ad esempio, impostando la `MessagePackSecurity.Active` proprietà statica su `MessagePackSecurity.UntrustedData` . L'impostazione `MessagePackSecurity.Active` di richiede l'installazione manuale [di una versione 1.9. x di MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). `MessagePack`L'installazione di 1.9. x aggiorna la versione SignalR utilizzata da. Quando `MessagePackSecurity.Active` non è impostato su `MessagePackSecurity.UntrustedData` , un client dannoso potrebbe causare un attacco Denial of Service. Impostare `MessagePackSecurity.Active` in `Program.Main` , come illustrato nel codice seguente:

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurare MessagePack nel client

> [!NOTE]
> JSON è abilitato per impostazione predefinita per i client supportati. I client possono supportare un solo protocollo. L'aggiunta del supporto di MessagePack sostituirà tutti i protocolli configurati in precedenza.

### <a name="net-client"></a>Client .NET

Per abilitare MessagePack nel client .NET, installare il `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacchetto e chiamare il `AddMessagePackProtocol` `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Questa `AddMessagePackProtocol` chiamata accetta un delegato per la configurazione delle opzioni esattamente come il server.

### <a name="javascript-client"></a>Client JavaScript

Il supporto di MessagePack per il client JavaScript viene fornito dal [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) pacchetto NPM. Installare il pacchetto eseguendo il comando seguente in una shell dei comandi:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Dopo l'installazione del pacchetto NPM, il modulo può essere usato direttamente tramite un caricatore di moduli JavaScript o importato nel browser facendo riferimento al file seguente:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

In un browser `msgpack5` è necessario fare riferimento anche alla raccolta. Usare un `<script>` tag per creare un riferimento. La libreria si trova in *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Quando si usa l' `<script>` elemento, l'ordine è importante. Se *signalr-protocol-msgpack.js* viene fatto riferimento prima *msgpack5.js*, si verifica un errore durante il tentativo di connessione a MessagePack. *signalr.js* è necessario anche prima di *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` Se si aggiunge a, il client viene configurato per l'utilizzo del protocollo MessagePack durante la connessione a un server.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Al momento non sono disponibili opzioni di configurazione per il protocollo MessagePack nel client JavaScript.

## <a name="messagepack-quirks"></a>Peculiarità di MessagePack

Quando si usa il protocollo dell'hub MessagePack, è necessario tenere presenti alcuni aspetti.

### <a name="messagepack-is-case-sensitive"></a>MessagePack fa distinzione tra maiuscole e minuscole

Il protocollo MessagePack fa distinzione tra maiuscole e minuscole. Si consideri, ad esempio, la classe C# seguente:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Quando si invia dal client JavaScript, è necessario usare i `PascalCased` nomi delle proprietà, perché la combinazione di maiuscole e minuscole deve corrispondere esattamente alla classe C#. Ad esempio:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

`camelCased`L'uso di nomi non verrà associato correttamente alla classe C#. Per aggirare questo problema, è possibile usare l' `Key` attributo per specificare un nome diverso per la proprietà MessagePack. Per ulteriori informazioni, vedere [la documentazione MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind non viene mantenuto durante la serializzazione/deserializzazione

Il protocollo MessagePack non fornisce un modo per codificare il `Kind` valore di un oggetto `DateTime` . Di conseguenza, quando si deserializza una data, il protocollo dell'hub MessagePack presuppone che la data in ingresso sia in formato UTC. Se si usano `DateTime` i valori nell'ora locale, è consigliabile eseguire la conversione in formato UTC prima di inviarli. Convertirli dall'ora UTC all'ora locale quando vengono ricevuti.

Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue non è supportato da MessagePack in JavaScript

La libreria [msgpack5](https://github.com/mcollina/msgpack5) usata dal SignalR client JavaScript non supporta il `timestamp96` tipo in MessagePack. Questo tipo viene usato per codificare i valori di data molto grandi (molto presto in passato o molto lontano in futuro). Il valore di `DateTime.MinValue` è `January 1, 0001` che deve essere codificato in un `timestamp96` valore. Per questo motivo, l'invio `DateTime.MinValue` a un client JavaScript non è supportato. Quando `DateTime.MinValue` viene ricevuto dal client JavaScript, viene generato l'errore seguente:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

In genere, `DateTime.MinValue` viene usato per codificare un valore "Missing" o `null` . Se è necessario codificare tale valore in MessagePack, utilizzare un `DateTime` valore Nullable ( `DateTime?` ) o codificare un valore distinto che `bool` indica se la data è presente.

Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Supporto di MessagePack nell'ambiente di compilazione "Ahead of Time"

La libreria [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilizzata dal client e dal server .NET utilizza la generazione del codice per ottimizzare la serializzazione. Di conseguenza, non è supportato per impostazione predefinita negli ambienti che usano la compilazione "Ahead of Time" (ad esempio, Novell iOS o Unity). È possibile usare MessagePack in questi ambienti mediante la "pre-generazione" del codice del serializzatore/deserializzatore. Per ulteriori informazioni, vedere [la documentazione MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Una volta creati in precedenza i serializzatori, è possibile registrarli usando il delegato di configurazione passato a `AddMessagePackProtocol` :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>I controlli del tipo sono più severi in MessagePack

Il protocollo dell'hub JSON eseguirà le conversioni dei tipi durante la deserializzazione. Se, ad esempio, l'oggetto in ingresso ha un valore della proprietà che è un numero ( `{ foo: 42 }` ), ma la proprietà nella classe .NET è di tipo `string` , il valore verrà convertito. Tuttavia, MessagePack non esegue questa conversione e genererà un'eccezione che può essere visualizzata nei log lato server (e nella console):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Per ulteriori informazioni su questa limitazione, vedere la pagina relativa al problema di GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Risorse correlate

* [Introduzione](xref:tutorials/signalr)
* [Client .NET](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)

::: moniker-end
