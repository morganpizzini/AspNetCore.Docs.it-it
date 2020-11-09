---
title: API per la protezione dei dati ASP.NET Core varie
author: rick-anderson
description: Informazioni sull'interfaccia di ISecret per la protezione dei dati ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: bd772b11300cca8896ed512da1cd12c49e6f104b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060755"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="250b6-103">API per la protezione dei dati ASP.NET Core varie</span><span class="sxs-lookup"><span data-stu-id="250b6-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="250b6-104">I tipi che implementano una delle interfacce seguenti devono essere thread-safe per più chiamanti.</span><span class="sxs-lookup"><span data-stu-id="250b6-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="250b6-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="250b6-105">ISecret</span></span>

<span data-ttu-id="250b6-106">L' `ISecret` interfaccia rappresenta un valore di segreto, ad esempio materiale della chiave crittografica.</span><span class="sxs-lookup"><span data-stu-id="250b6-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="250b6-107">Contiene la seguente superficie API:</span><span class="sxs-lookup"><span data-stu-id="250b6-107">It contains the following API surface:</span></span>

* <span data-ttu-id="250b6-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="250b6-108">`Length`: `int`</span></span>

* <span data-ttu-id="250b6-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="250b6-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="250b6-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="250b6-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="250b6-111">Il `WriteSecretIntoBuffer` metodo popola il buffer fornito con il valore del segreto non elaborato.</span><span class="sxs-lookup"><span data-stu-id="250b6-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="250b6-112">Il motivo per cui questa API accetta il buffer come parametro anziché restituire `byte[]` direttamente un oggetto è che questo fornisce al chiamante la possibilità di aggiungere l'oggetto buffer, limitando l'esposizione segreta al Garbage Collector gestito.</span><span class="sxs-lookup"><span data-stu-id="250b6-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="250b6-113">Il `Secret` tipo è un'implementazione concreta di `ISecret` in cui il valore del segreto viene archiviato nella memoria in-process.</span><span class="sxs-lookup"><span data-stu-id="250b6-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="250b6-114">Nelle piattaforme Windows il valore Secret viene crittografato tramite [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span><span class="sxs-lookup"><span data-stu-id="250b6-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
