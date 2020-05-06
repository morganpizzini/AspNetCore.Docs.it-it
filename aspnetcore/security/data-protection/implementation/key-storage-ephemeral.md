---
title: Provider di protezione dati temporanei in ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione dei provider di protezione dati temporanei ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: 22a332230e15256dc33fd1d06f2da3ea8d34d3bc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776890"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>Provider di protezione dati temporanei in ASP.NET Core

<a name="data-protection-implementation-key-storage-ephemeral"></a>

Esistono scenari in cui un'applicazione necessita di uno `IDataProtectionProvider`stesso. È ad esempio possibile che lo sviluppatore stia semplicemente sperimentando un'applicazione console monouso o che l'applicazione stessa sia temporanea, ovvero che sia costituita da script o da un progetto unit test. Per supportare questi scenari, il pacchetto [Microsoft. AspNetCore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) include un `EphemeralDataProtectionProvider`tipo. Questo tipo fornisce un'implementazione di base `IDataProtectionProvider` di il cui repository di chiavi viene mantenuto esclusivamente in memoria e non viene scritto in alcun archivio di backup.

In ogni istanza `EphemeralDataProtectionProvider` di viene utilizzata la propria chiave master univoca. Di conseguenza, se `IDataProtector` un oggetto rooted `EphemeralDataProtectionProvider` in un oggetto genera un payload protetto, il payload può essere solo protetto da un `IDataProtector` oggetto equivalente (dato la stessa catena di [scopi](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) ) radice nella `EphemeralDataProtectionProvider` stessa istanza.

Nell'esempio seguente viene illustrata la `EphemeralDataProtectionProvider` creazione di un'istanza di e la relativa utilizzo per proteggere e rimuovere la protezione dei dati.

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
