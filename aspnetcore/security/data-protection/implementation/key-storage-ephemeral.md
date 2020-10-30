---
title: Provider di protezione dati temporanei in ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione dei provider di protezione dati temporanei ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: ed0fca88ecf2b002a4421fb120d90adff1b5b12e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052699"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="b445a-103">Provider di protezione dati temporanei in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b445a-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="b445a-104">Esistono scenari in cui un'applicazione necessita di uno stesso `IDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="b445a-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="b445a-105">È ad esempio possibile che lo sviluppatore stia semplicemente sperimentando un'applicazione console monouso o che l'applicazione stessa sia temporanea, ovvero che sia costituita da script o da un progetto unit test.</span><span class="sxs-lookup"><span data-stu-id="b445a-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="b445a-106">Per supportare questi scenari, il pacchetto [Microsoft. AspNetCore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) include un tipo `EphemeralDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="b445a-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="b445a-107">Questo tipo fornisce un'implementazione di base di il `IDataProtectionProvider` cui repository di chiavi viene mantenuto esclusivamente in memoria e non viene scritto in alcun archivio di backup.</span><span class="sxs-lookup"><span data-stu-id="b445a-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="b445a-108">In ogni istanza di `EphemeralDataProtectionProvider` viene utilizzata la propria chiave master univoca.</span><span class="sxs-lookup"><span data-stu-id="b445a-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="b445a-109">Di conseguenza, se un oggetto `IDataProtector` rooted in un oggetto `EphemeralDataProtectionProvider` genera un payload protetto, il payload può essere solo protetto da un oggetto equivalente `IDataProtector` (dato la stessa catena di [scopi](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) ) radice nella stessa `EphemeralDataProtectionProvider` istanza.</span><span class="sxs-lookup"><span data-stu-id="b445a-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="b445a-110">Nell'esempio seguente viene illustrata la creazione di un'istanza `EphemeralDataProtectionProvider` di e la relativa utilizzo per proteggere e rimuovere la protezione dei dati.</span><span class="sxs-lookup"><span data-stu-id="b445a-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

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
