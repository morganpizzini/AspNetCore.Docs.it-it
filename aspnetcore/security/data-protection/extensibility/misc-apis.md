---
title: API per la protezione dei dati ASP.NET Core varie
author: rick-anderson
description: Informazioni sull'interfaccia di ISecret per la protezione dei dati ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: e9de92233468e9e07791df608b1c37ffb3b29949
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408500"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="57f5e-103">API per la protezione dei dati ASP.NET Core varie</span><span class="sxs-lookup"><span data-stu-id="57f5e-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="57f5e-104">I tipi che implementano una delle interfacce seguenti devono essere thread-safe per più chiamanti.</span><span class="sxs-lookup"><span data-stu-id="57f5e-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="57f5e-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="57f5e-105">ISecret</span></span>

<span data-ttu-id="57f5e-106">L' `ISecret` interfaccia rappresenta un valore di segreto, ad esempio materiale della chiave crittografica.</span><span class="sxs-lookup"><span data-stu-id="57f5e-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="57f5e-107">Contiene la seguente superficie API:</span><span class="sxs-lookup"><span data-stu-id="57f5e-107">It contains the following API surface:</span></span>

* <span data-ttu-id="57f5e-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="57f5e-108">`Length`: `int`</span></span>

* <span data-ttu-id="57f5e-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="57f5e-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="57f5e-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="57f5e-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="57f5e-111">Il `WriteSecretIntoBuffer` metodo popola il buffer fornito con il valore del segreto non elaborato.</span><span class="sxs-lookup"><span data-stu-id="57f5e-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="57f5e-112">Il motivo per cui questa API accetta il buffer come parametro anziché restituire `byte[]` direttamente un oggetto è che questo fornisce al chiamante la possibilità di aggiungere l'oggetto buffer, limitando l'esposizione segreta al Garbage Collector gestito.</span><span class="sxs-lookup"><span data-stu-id="57f5e-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="57f5e-113">Il `Secret` tipo è un'implementazione concreta di `ISecret` in cui il valore del segreto viene archiviato nella memoria in-process.</span><span class="sxs-lookup"><span data-stu-id="57f5e-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="57f5e-114">Nelle piattaforme Windows il valore Secret viene crittografato tramite [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span><span class="sxs-lookup"><span data-stu-id="57f5e-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
