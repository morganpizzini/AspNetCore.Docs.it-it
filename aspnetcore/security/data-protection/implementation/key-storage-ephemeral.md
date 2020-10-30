---
title: Provider di protezione dati temporanei in ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione dei provider di protezione dati temporanei ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: ed0fca88ecf2b002a4421fb120d90adff1b5b12e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052699"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>Provider di protezione dati temporanei in ASP.NET Core

<a name="data-protection-implementation-key-storage-ephemeral"></a>

Esistono scenari in cui un'applicazione necessita di uno stesso `IDataProtectionProvider` . È ad esempio possibile che lo sviluppatore stia semplicemente sperimentando un'applicazione console monouso o che l'applicazione stessa sia temporanea, ovvero che sia costituita da script o da un progetto unit test. Per supportare questi scenari, il pacchetto [Microsoft. AspNetCore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) include un tipo `EphemeralDataProtectionProvider` . Questo tipo fornisce un'implementazione di base di il `IDataProtectionProvider` cui repository di chiavi viene mantenuto esclusivamente in memoria e non viene scritto in alcun archivio di backup.

In ogni istanza di `EphemeralDataProtectionProvider` viene utilizzata la propria chiave master univoca. Di conseguenza, se un oggetto `IDataProtector` rooted in un oggetto `EphemeralDataProtectionProvider` genera un payload protetto, il payload può essere solo protetto da un oggetto equivalente `IDataProtector` (dato la stessa catena di [scopi](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) ) radice nella stessa `EphemeralDataProtectionProvider` istanza.

Nell'esempio seguente viene illustrata la creazione di un'istanza `EphemeralDataProtectionProvider` di e la relativa utilizzo per proteggere e rimuovere la protezione dei dati.

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
