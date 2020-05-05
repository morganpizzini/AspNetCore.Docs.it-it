---
title: Schemi di criteri in ASP.NET Core
author: rick-anderson
description: Gli schemi dei criteri di autenticazione rendono più semplice avere un unico schema di autenticazione logica
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/policyschemes
ms.openlocfilehash: ddedf62c5e8363bd93c9948fd2d3418abc566539
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767317"
---
# <a name="policy-schemes-in-aspnet-core"></a>Schemi di criteri in ASP.NET Core

Gli schemi dei criteri di autenticazione rendono più semplice disporre di un singolo schema di autenticazione logica che potenzialmente utilizza diversi approcci. Ad esempio, uno schema di criteri può utilizzare l'autenticazione di Google per le richieste di problemi e l'autenticazione dei cookie per tutti gli altri elementi. Gli schemi dei criteri di autenticazione lo rendono:

* Facile da inviare qualsiasi azione di autenticazione a un altro schema.
* In avanti in modo dinamico in base alla richiesta.

Tutti gli schemi di autenticazione che <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> utilizzano le [>e\< ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)derivate e AuthenticationHandler associate:

* Gli schemi di criteri sono automaticamente in ASP.NET Core 2,1 e versioni successive.
* Può essere abilitato tramite la configurazione delle opzioni dello schema.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Esempi

Nell'esempio seguente viene illustrato uno schema di livello superiore che combina schemi di livello inferiore. L'autenticazione di Google viene utilizzata per le richieste e l'autenticazione dei cookie viene utilizzata per tutte le altre operazioni:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

Nell'esempio seguente viene abilitata la selezione dinamica degli schemi in base alle singole richieste. Ovvero come combinare cookie e autenticazione API:

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
