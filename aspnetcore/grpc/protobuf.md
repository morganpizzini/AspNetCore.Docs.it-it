---
title: Creare messaggi protobuf per le app .NET
author: jamesnk
description: Informazioni su come creare messaggi protobuf per le app .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
no-loc:
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
uid: grpc/protobuf
ms.openlocfilehash: 60af1add9ae2f8b2b94bc19b65667d7af91fb122
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102666"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="7134b-103">Creare messaggi protobuf per le app .NET</span><span class="sxs-lookup"><span data-stu-id="7134b-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="7134b-104">Di [James Newton-King](https://twitter.com/jamesnk) e [Mark Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="7134b-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="7134b-105">gRPC USA [protobuf](https://developers.google.com/protocol-buffers) come linguaggio di definizione dell'interfaccia (IDL).</span><span class="sxs-lookup"><span data-stu-id="7134b-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="7134b-106">Protobuf IDL è un formato indipendente dal linguaggio per la specifica dei messaggi inviati e ricevuti dai servizi di gRPC.</span><span class="sxs-lookup"><span data-stu-id="7134b-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="7134b-107">I messaggi protobuf sono definiti nei `.proto` file.</span><span class="sxs-lookup"><span data-stu-id="7134b-107">Protobuf messages are defined in `.proto` files.</span></span> <span data-ttu-id="7134b-108">Questo documento illustra come i concetti di protobuf vengono mappati a .NET.</span><span class="sxs-lookup"><span data-stu-id="7134b-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="7134b-109">Messaggi protobuf</span><span class="sxs-lookup"><span data-stu-id="7134b-109">Protobuf messages</span></span>

<span data-ttu-id="7134b-110">I messaggi sono l'oggetto principale per il trasferimento dei dati in protobuf.</span><span class="sxs-lookup"><span data-stu-id="7134b-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="7134b-111">Sono concettualmente simili alle classi .NET.</span><span class="sxs-lookup"><span data-stu-id="7134b-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="7134b-112">La definizione di messaggio precedente specifica tre campi come coppie nome-valore.</span><span class="sxs-lookup"><span data-stu-id="7134b-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="7134b-113">Analogamente alle proprietà sui tipi .NET, ogni campo dispone di un nome e di un tipo.</span><span class="sxs-lookup"><span data-stu-id="7134b-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="7134b-114">Il tipo di campo può essere un [tipo di valore scalare protobuf](#scalar-value-types), ad esempio `int32` o un altro messaggio.</span><span class="sxs-lookup"><span data-stu-id="7134b-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="7134b-115">Oltre a un nome, ogni campo nella definizione del messaggio ha un numero univoco.</span><span class="sxs-lookup"><span data-stu-id="7134b-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="7134b-116">I numeri di campo vengono utilizzati per identificare i campi quando il messaggio viene serializzato in protobuf.</span><span class="sxs-lookup"><span data-stu-id="7134b-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="7134b-117">La serializzazione di un numero ridotto è più veloce rispetto alla serializzazione dell'intero nome di campo.</span><span class="sxs-lookup"><span data-stu-id="7134b-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="7134b-118">Poiché i numeri di campo identificano un campo, è importante prestare attenzione durante la modifica.</span><span class="sxs-lookup"><span data-stu-id="7134b-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="7134b-119">Per ulteriori informazioni sulla modifica dei messaggi protobuf <xref:grpc/versioning> , vedere.</span><span class="sxs-lookup"><span data-stu-id="7134b-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="7134b-120">Quando viene compilata un'app, gli strumenti protobuf generano tipi .NET dai `.proto` file.</span><span class="sxs-lookup"><span data-stu-id="7134b-120">When an app is built the Protobuf tooling generates .NET types from `.proto` files.</span></span> <span data-ttu-id="7134b-121">Il `Person` messaggio genera una classe .NET:</span><span class="sxs-lookup"><span data-stu-id="7134b-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="7134b-122">Per ulteriori informazioni sui messaggi protobuf, vedere la [Guida alla lingua di protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span><span class="sxs-lookup"><span data-stu-id="7134b-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="7134b-123">Tipi di valore scalari</span><span class="sxs-lookup"><span data-stu-id="7134b-123">Scalar Value Types</span></span>

<span data-ttu-id="7134b-124">Protobuf supporta un intervallo di tipi valore scalari nativi.</span><span class="sxs-lookup"><span data-stu-id="7134b-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="7134b-125">La tabella seguente elenca tutti i tipi con il tipo C# equivalente:</span><span class="sxs-lookup"><span data-stu-id="7134b-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="7134b-126">Tipo protobuf</span><span class="sxs-lookup"><span data-stu-id="7134b-126">Protobuf type</span></span> | <span data-ttu-id="7134b-127">Tipo C#</span><span class="sxs-lookup"><span data-stu-id="7134b-127">C# type</span></span>      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

### <a name="dates-and-times"></a><span data-ttu-id="7134b-128">Date e ore</span><span class="sxs-lookup"><span data-stu-id="7134b-128">Dates and times</span></span>

<span data-ttu-id="7134b-129">I tipi scalari nativi non forniscono i valori di data e ora, equivalenti a. NET <xref:System.DateTimeOffset> , <xref:System.DateTime> e <xref:System.TimeSpan> .</span><span class="sxs-lookup"><span data-stu-id="7134b-129">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="7134b-130">Questi tipi possono essere specificati usando alcune estensioni dei *tipi noti* di protobuf.</span><span class="sxs-lookup"><span data-stu-id="7134b-130">These types can be specified by using some of Protobuf's *Well-Known Types* extensions.</span></span> <span data-ttu-id="7134b-131">Queste estensioni forniscono il supporto per la generazione di codice e il runtime per i tipi di campo complessi nelle piattaforme supportate.</span><span class="sxs-lookup"><span data-stu-id="7134b-131">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="7134b-132">La tabella seguente illustra i tipi di data e ora:</span><span class="sxs-lookup"><span data-stu-id="7134b-132">The following table shows the date and time types:</span></span>

| <span data-ttu-id="7134b-133">Tipo .NET</span><span class="sxs-lookup"><span data-stu-id="7134b-133">.NET type</span></span>        | <span data-ttu-id="7134b-134">Tipo noto protobuf</span><span class="sxs-lookup"><span data-stu-id="7134b-134">Protobuf Well-Known Type</span></span>    |
| ---------------- | --------------------------- |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime`       | `google.protobuf.Timestamp` |
| `TimeSpan`       | `google.protobuf.Duration`  |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

<span data-ttu-id="7134b-135">Le proprietà generate nella classe C# non sono i tipi di data e ora .NET.</span><span class="sxs-lookup"><span data-stu-id="7134b-135">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="7134b-136">Le proprietà utilizzano le `Timestamp` `Duration` classi e nello `Google.Protobuf.WellKnownTypes` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="7134b-136">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="7134b-137">Queste classi forniscono metodi per la conversione da e verso `DateTimeOffset` , `DateTime` e `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="7134b-137">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> <span data-ttu-id="7134b-138">Il `Timestamp` tipo funziona con l'ora UTC.</span><span class="sxs-lookup"><span data-stu-id="7134b-138">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="7134b-139">`DateTimeOffset` i valori hanno sempre un offset pari a zero e la `DateTime.Kind` proprietà è sempre `DateTimeKind.Utc` .</span><span class="sxs-lookup"><span data-stu-id="7134b-139">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="7134b-140">Tipi nullable</span><span class="sxs-lookup"><span data-stu-id="7134b-140">Nullable types</span></span>

<span data-ttu-id="7134b-141">La generazione di codice protobuf per C# usa i tipi nativi, ad esempio `int` per `int32` .</span><span class="sxs-lookup"><span data-stu-id="7134b-141">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="7134b-142">Pertanto, i valori vengono sempre inclusi e non possono essere `null` .</span><span class="sxs-lookup"><span data-stu-id="7134b-142">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="7134b-143">Per i valori che richiedono esplicito `null` , ad esempio l'uso `int?` di nel codice c#, i tipi noti di protobuf includono wrapper compilati in tipi c# Nullable.</span><span class="sxs-lookup"><span data-stu-id="7134b-143">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's Well-Known Types include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="7134b-144">Per usarli, importare `wrappers.proto` nel `.proto` file, come nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="7134b-144">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="7134b-145">Protobuf utilizza i tipi nullable .NET, ad esempio, `int?` per la proprietà del messaggio generata.</span><span class="sxs-lookup"><span data-stu-id="7134b-145">Protobuf uses .NET nullable types, for example, `int?`, for the generated message property.</span></span>

<span data-ttu-id="7134b-146">La tabella seguente mostra l'elenco completo dei tipi di wrapper con il tipo C# equivalente:</span><span class="sxs-lookup"><span data-stu-id="7134b-146">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="7134b-147">Tipo C#</span><span class="sxs-lookup"><span data-stu-id="7134b-147">C# type</span></span>   | <span data-ttu-id="7134b-148">Wrapper di tipi noti</span><span class="sxs-lookup"><span data-stu-id="7134b-148">Well-Known Type wrapper</span></span>       |
| --------- | ----------------------------- |
| `bool?`   | `google.protobuf.BoolValue`   |
| `double?` | `google.protobuf.DoubleValue` |
| `float?`  | `google.protobuf.FloatValue`  |
| `int?`    | `google.protobuf.Int32Value`  |
| `long?`   | `google.protobuf.Int64Value`  |
| `uint?`   | `google.protobuf.UInt32Value` |
| `ulong?`  | `google.protobuf.UInt64Value` |

### <a name="decimals"></a><span data-ttu-id="7134b-149">Decimali</span><span class="sxs-lookup"><span data-stu-id="7134b-149">Decimals</span></span>

<span data-ttu-id="7134b-150">Protobuf non supporta in modo nativo il `decimal` tipo .NET, solo `double` e `float` .</span><span class="sxs-lookup"><span data-stu-id="7134b-150">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="7134b-151">Nel progetto protobuf è presente una discussione continuativa sulla possibilità di aggiungere un tipo decimale standard ai tipi noti, con supporto della piattaforma per linguaggi e Framework che lo supportano.</span><span class="sxs-lookup"><span data-stu-id="7134b-151">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the Well-Known Types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="7134b-152">Non è stato ancora implementato alcun elemento.</span><span class="sxs-lookup"><span data-stu-id="7134b-152">Nothing has been implemented yet.</span></span>

<span data-ttu-id="7134b-153">È possibile creare una definizione di messaggio per rappresentare il `decimal` tipo che funziona per la serializzazione sicura tra client e server .NET.</span><span class="sxs-lookup"><span data-stu-id="7134b-153">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="7134b-154">Gli sviluppatori su altre piattaforme dovrebbero tuttavia comprendere il formato usato e implementare la propria gestione per l'it.</span><span class="sxs-lookup"><span data-stu-id="7134b-154">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="7134b-155">Creazione di un tipo decimale personalizzato per protobuf</span><span class="sxs-lookup"><span data-stu-id="7134b-155">Creating a custom decimal type for Protobuf</span></span>

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

<span data-ttu-id="7134b-156">Il `nanos` campo rappresenta i valori da `0.999_999_999` a `-0.999_999_999` .</span><span class="sxs-lookup"><span data-stu-id="7134b-156">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="7134b-157">Il valore, ad esempio, viene `decimal` `1.5m` rappresentato come `{ units = 1, nanos = 500_000_000 }` .</span><span class="sxs-lookup"><span data-stu-id="7134b-157">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="7134b-158">Questo è il motivo `nanos` per cui il campo in questo esempio usa il `sfixed32` tipo, che codifica in modo più efficiente rispetto `int32` ai valori più grandi.</span><span class="sxs-lookup"><span data-stu-id="7134b-158">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="7134b-159">Se il `units` campo è negativo, `nanos` anche il campo dovrebbe essere negativo.</span><span class="sxs-lookup"><span data-stu-id="7134b-159">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="7134b-160">Sono disponibili più algoritmi per la codifica `decimal` dei valori come stringhe di byte, ma questo messaggio è più facile da comprendere.</span><span class="sxs-lookup"><span data-stu-id="7134b-160">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="7134b-161">I valori non sono interessati da Big-endian o little-endian su piattaforme diverse.</span><span class="sxs-lookup"><span data-stu-id="7134b-161">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="7134b-162">La conversione tra questo tipo e il `decimal` tipo BCL potrebbe essere implementata in C# come segue:</span><span class="sxs-lookup"><span data-stu-id="7134b-162">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a><span data-ttu-id="7134b-163">Raccolte</span><span class="sxs-lookup"><span data-stu-id="7134b-163">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="7134b-164">Elenchi</span><span class="sxs-lookup"><span data-stu-id="7134b-164">Lists</span></span>

<span data-ttu-id="7134b-165">Gli elenchi in protobuf vengono specificati utilizzando la `repeated` parola chiave prefix in un campo.</span><span class="sxs-lookup"><span data-stu-id="7134b-165">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="7134b-166">Nell'esempio seguente viene illustrato come creare un elenco:</span><span class="sxs-lookup"><span data-stu-id="7134b-166">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="7134b-167">Nel codice generato, `repeated` i campi sono rappresentati dal `Google.Protobuf.Collections.RepeatedField<T>` tipo generico.</span><span class="sxs-lookup"><span data-stu-id="7134b-167">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="7134b-168">`RepeatedField<T>` implementa <xref:System.Collections.Generic.IList%601>.</span><span class="sxs-lookup"><span data-stu-id="7134b-168">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="7134b-169">Quindi, è possibile usare le query LINQ o convertirle in una matrice o in un elenco.</span><span class="sxs-lookup"><span data-stu-id="7134b-169">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="7134b-170">`RepeatedField<T>` le proprietà non hanno un setter pubblico.</span><span class="sxs-lookup"><span data-stu-id="7134b-170">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="7134b-171">Gli elementi devono essere aggiunti alla raccolta esistente.</span><span class="sxs-lookup"><span data-stu-id="7134b-171">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="7134b-172">Dizionari</span><span class="sxs-lookup"><span data-stu-id="7134b-172">Dictionaries</span></span>

<span data-ttu-id="7134b-173">Il <xref:System.Collections.Generic.IDictionary%602> tipo .NET è rappresentato in protobuf usando `map<key_type, value_type>` .</span><span class="sxs-lookup"><span data-stu-id="7134b-173">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="7134b-174">Nel codice .NET generato, `map` i campi sono rappresentati dal `Google.Protobuf.Collections.MapField<TKey, TValue>` tipo generico.</span><span class="sxs-lookup"><span data-stu-id="7134b-174">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="7134b-175">`MapField<TKey, TValue>` implementa <xref:System.Collections.Generic.IDictionary%602>.</span><span class="sxs-lookup"><span data-stu-id="7134b-175">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="7134b-176">Analogamente alle `repeated` proprietà, le `map` proprietà non hanno un setter pubblico.</span><span class="sxs-lookup"><span data-stu-id="7134b-176">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="7134b-177">Gli elementi devono essere aggiunti alla raccolta esistente.</span><span class="sxs-lookup"><span data-stu-id="7134b-177">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="7134b-178">Messaggi non strutturati e condizionali</span><span class="sxs-lookup"><span data-stu-id="7134b-178">Unstructured and conditional messages</span></span>

<span data-ttu-id="7134b-179">Protobuf è un formato di messaggistica prima del contratto.</span><span class="sxs-lookup"><span data-stu-id="7134b-179">Protobuf is a contract-first messaging format.</span></span> <span data-ttu-id="7134b-180">I messaggi di un'app, inclusi i campi e i tipi, devono essere specificati nei `.proto` file quando viene compilata l'app.</span><span class="sxs-lookup"><span data-stu-id="7134b-180">An app's messages, including its fields and types, must be specified in `.proto` files when the app is built.</span></span> <span data-ttu-id="7134b-181">La progettazione dei contratti di protobuf è ideale per applicare il contenuto dei messaggi, ma può limitare gli scenari in cui non è necessario un contratto rigido:</span><span class="sxs-lookup"><span data-stu-id="7134b-181">Protobuf's contract-first design is great at enforcing message content but can limit scenarios where a strict contract isn't required:</span></span>

* <span data-ttu-id="7134b-182">Messaggi con payload sconosciuti.</span><span class="sxs-lookup"><span data-stu-id="7134b-182">Messages with unknown payloads.</span></span> <span data-ttu-id="7134b-183">Ad esempio, un messaggio con un campo che può contenere qualsiasi messaggio.</span><span class="sxs-lookup"><span data-stu-id="7134b-183">For example, a message with a field that could contain any message.</span></span>
* <span data-ttu-id="7134b-184">Messaggi condizionali.</span><span class="sxs-lookup"><span data-stu-id="7134b-184">Conditional messages.</span></span> <span data-ttu-id="7134b-185">Un messaggio restituito da un servizio gRPC, ad esempio, potrebbe essere un risultato positivo o un errore.</span><span class="sxs-lookup"><span data-stu-id="7134b-185">For example, a message returned from a gRPC service might be a success result or an error result.</span></span>
* <span data-ttu-id="7134b-186">Valori dinamici.</span><span class="sxs-lookup"><span data-stu-id="7134b-186">Dynamic values.</span></span> <span data-ttu-id="7134b-187">Ad esempio, un messaggio con un campo che contiene una raccolta non strutturata di valori, simile a JSON.</span><span class="sxs-lookup"><span data-stu-id="7134b-187">For example, a message with a field that contains an unstructured collection of values, similar to JSON.</span></span>

<span data-ttu-id="7134b-188">Protobuf offre funzionalità del linguaggio e tipi per supportare questi scenari.</span><span class="sxs-lookup"><span data-stu-id="7134b-188">Protobuf offers language features and types to support these scenarios.</span></span>

### <a name="any"></a><span data-ttu-id="7134b-189">Qualsiasi</span><span class="sxs-lookup"><span data-stu-id="7134b-189">Any</span></span>

<span data-ttu-id="7134b-190">Il `Any` tipo consente di usare i messaggi come tipi incorporati senza avere la relativa `.proto` definizione.</span><span class="sxs-lookup"><span data-stu-id="7134b-190">The `Any` type lets you use messages as embedded types without having their `.proto` definition.</span></span> <span data-ttu-id="7134b-191">Per utilizzare il `Any` tipo, Import `any.proto` .</span><span class="sxs-lookup"><span data-stu-id="7134b-191">To use the `Any` type, import `any.proto`.</span></span>

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a><span data-ttu-id="7134b-192">Di</span><span class="sxs-lookup"><span data-stu-id="7134b-192">Oneof</span></span>

<span data-ttu-id="7134b-193">`oneof` i campi sono una funzionalità del linguaggio.</span><span class="sxs-lookup"><span data-stu-id="7134b-193">`oneof` fields are a language feature.</span></span> <span data-ttu-id="7134b-194">Il compilatore gestisce la `oneof` parola chiave quando genera la classe Message.</span><span class="sxs-lookup"><span data-stu-id="7134b-194">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="7134b-195">`oneof`L'utilizzo di per specificare un messaggio di risposta che può restituire `Person` o potrebbe essere simile al `Error` seguente:</span><span class="sxs-lookup"><span data-stu-id="7134b-195">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

<span data-ttu-id="7134b-196">I campi all'interno del `oneof` set devono avere numeri di campo univoci nella dichiarazione generale del messaggio.</span><span class="sxs-lookup"><span data-stu-id="7134b-196">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="7134b-197">Quando `oneof` si usa, il codice C# generato include un'enumerazione che specifica quale dei campi è stata impostata.</span><span class="sxs-lookup"><span data-stu-id="7134b-197">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="7134b-198">È possibile testare l'enumerazione per individuare il campo da impostare.</span><span class="sxs-lookup"><span data-stu-id="7134b-198">You can test the enum to find which field is set.</span></span> <span data-ttu-id="7134b-199">I campi che non sono impostati restituiscono `null` o il valore predefinito, anziché generare un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="7134b-199">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a><span data-ttu-id="7134b-200">valore</span><span class="sxs-lookup"><span data-stu-id="7134b-200">Value</span></span>

<span data-ttu-id="7134b-201">Il `Value` tipo rappresenta un valore tipizzato dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="7134b-201">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="7134b-202">Può essere `null` , un numero, una stringa, un valore booleano, un dizionario di valori ( `Struct` ) o un elenco di valori ( `ValueList` ).</span><span class="sxs-lookup"><span data-stu-id="7134b-202">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="7134b-203">`Value` è un tipo noto protobuf che usa la funzionalità descritta in precedenza `oneof` .</span><span class="sxs-lookup"><span data-stu-id="7134b-203">`Value` is a Protobuf Well-Known Type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="7134b-204">Per utilizzare il `Value` tipo, Import `struct.proto` .</span><span class="sxs-lookup"><span data-stu-id="7134b-204">To use the `Value` type, import `struct.proto`.</span></span>

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

<span data-ttu-id="7134b-205">L'uso `Value` diretto può essere dettagliato.</span><span class="sxs-lookup"><span data-stu-id="7134b-205">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="7134b-206">Un modo alternativo per usare `Value` è il supporto incorporato di protobuf per il mapping dei messaggi a JSON.</span><span class="sxs-lookup"><span data-stu-id="7134b-206">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="7134b-207">`JsonFormatter`I tipi e di protobuf `JsonWriter` possono essere usati con qualsiasi messaggio protobuf.</span><span class="sxs-lookup"><span data-stu-id="7134b-207">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="7134b-208">`Value` è particolarmente adatto per essere convertito in e da JSON.</span><span class="sxs-lookup"><span data-stu-id="7134b-208">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="7134b-209">Si tratta dell'equivalente JSON del codice precedente:</span><span class="sxs-lookup"><span data-stu-id="7134b-209">This is the JSON equivalent of the previous code:</span></span>

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a><span data-ttu-id="7134b-210">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7134b-210">Additional resources</span></span>

* [<span data-ttu-id="7134b-211">Guida alla lingua di protobuf</span><span class="sxs-lookup"><span data-stu-id="7134b-211">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
