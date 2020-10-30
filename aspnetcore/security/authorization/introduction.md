---
title: Introduzione all'autorizzazione in ASP.NET Core
author: rick-anderson
description: Informazioni sulle nozioni di base sull'autorizzazione e sul funzionamento dell'autorizzazione nelle app ASP.NET Core.
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
uid: security/authorization/introduction
ms.openlocfilehash: 8afee7d8bc6b7ad71154916f4a41a2b417b24f9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060248"
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
