---
title: Introduzione all'autorizzazione in ASP.NET Core
author: rick-anderson
description: Informazioni sulle nozioni di base sull'autorizzazione e sul funzionamento dell'autorizzazione nelle app ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/introduction
ms.openlocfilehash: 7d7570ead1365588fd582d9bea364685da29a576
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634436"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>Introduzione all'autorizzazione in ASP.NET Core

<a name="security-authorization-introduction"></a>

L'autorizzazione si riferisce al processo che determina ciò che un utente è in grado di eseguire. Ad esempio, un utente amministratore può creare una raccolta documenti, aggiungere documenti, modificare documenti ed eliminarli. Un utente non amministratore che utilizza la libreria è autorizzato solo a leggere i documenti.

L'autorizzazione è ortogonale e indipendente dall'autenticazione. Tuttavia, l'autorizzazione richiede un meccanismo di autenticazione. L'autenticazione è il processo che consente di verificare l'identità di un utente. L'autenticazione, inoltre, può creare una o più identità per l'utente corrente.

Per ulteriori informazioni sull'autenticazione in ASP.NET Core, vedere <xref:security/authentication/index> .

## <a name="authorization-types"></a>Tipi di autorizzazione

ASP.NET Core autorizzazione fornisce un [ruolo](xref:security/authorization/roles) semplice e dichiarativo e un modello avanzato [basato sui criteri](xref:security/authorization/policies) . L'autorizzazione è espressa nei requisiti e i gestori valutano le attestazioni di un utente in base ai requisiti. I controlli imperativi possono essere basati su criteri semplici o criteri che valutano l'identità dell'utente e le proprietà della risorsa a cui l'utente sta tentando di accedere.

## <a name="namespaces"></a>Spazi dei nomi

I componenti di autorizzazione, inclusi gli `AuthorizeAttribute` `AllowAnonymousAttribute` attributi e, si trovano nello `Microsoft.AspNetCore.Authorization` spazio dei nomi.

Consultare la documentazione relativa all' [autorizzazione semplice](xref:security/authorization/simple).
