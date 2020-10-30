---
title: Password hash in ASP.NET Core
author: rick-anderson
description: Informazioni su come eseguire l'hashing delle password usando le API di protezione dei dati ASP.NET Core.
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
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: a970d44a1ca6b9f3534bddb34b037e7c2fdc5389
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051863"
---
# <a name="hash-passwords-in-aspnet-core"></a>Password hash in ASP.NET Core

La base di codice di protezione dei dati include un pacchetto *Microsoft. AspNetCore. Cryptography. Key Derivation* che contiene funzioni di derivazione della chiave crittografica. Questo pacchetto è un componente autonomo e non ha dipendenze dal resto del sistema di protezione dei dati. Può essere usato completamente in modo indipendente. L'origine esiste insieme alla base del codice di protezione dei dati per praticità.

Il pacchetto offre attualmente un metodo `KeyDerivation.Pbkdf2` che consente l'hashing di una password usando l' [algoritmo PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2). Questa API è molto simile al [tipo Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes)esistente del .NET Framework, ma sono disponibili tre distinzioni importanti:

1. Il `KeyDerivation.Pbkdf2` metodo supporta l'utilizzo di più PRFs (attualmente `HMACSHA1` , `HMACSHA256` e `HMACSHA512` ), mentre il `Rfc2898DeriveBytes` tipo supporta solo `HMACSHA1` .

2. Il `KeyDerivation.Pbkdf2` metodo rileva il sistema operativo corrente e tenta di scegliere l'implementazione più ottimizzata della routine, garantendo in alcuni casi prestazioni molto migliori. In Windows 8 offre circa 10 volte la velocità effettiva di `Rfc2898DeriveBytes` .

3. Il `KeyDerivation.Pbkdf2` metodo richiede che il chiamante specifichi tutti i parametri (Salt, PRF e count di iterazione). Il `Rfc2898DeriveBytes` tipo fornisce i valori predefiniti per questi.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Per un caso d'uso reale, vedere il [codice sorgente](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) per il ASP.NET Core Identity `PasswordHasher` tipo di.
