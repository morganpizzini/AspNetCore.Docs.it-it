---
title: API per la protezione dei dati ASP.NET Core varie
author: rick-anderson
description: Informazioni sull'interfaccia di ISecret per la protezione dei dati ASP.NET Core.
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
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: bd772b11300cca8896ed512da1cd12c49e6f104b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060755"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>API per la protezione dei dati ASP.NET Core varie

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> I tipi che implementano una delle interfacce seguenti devono essere thread-safe per più chiamanti.

## <a name="isecret"></a>ISecret

L' `ISecret` interfaccia rappresenta un valore di segreto, ad esempio materiale della chiave crittografica. Contiene la seguente superficie API:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

Il `WriteSecretIntoBuffer` metodo popola il buffer fornito con il valore del segreto non elaborato. Il motivo per cui questa API accetta il buffer come parametro anziché restituire `byte[]` direttamente un oggetto è che questo fornisce al chiamante la possibilità di aggiungere l'oggetto buffer, limitando l'esposizione segreta al Garbage Collector gestito.

Il `Secret` tipo è un'implementazione concreta di `ISecret` in cui il valore del segreto viene archiviato nella memoria in-process. Nelle piattaforme Windows il valore Secret viene crittografato tramite [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).
