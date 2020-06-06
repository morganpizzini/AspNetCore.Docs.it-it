---
title: Esegui la migrazione da ClaimsPrincipal. Current
author: mjrousos
description: Informazioni su come eseguire la migrazione da ClaimsPrincipal. Current per recuperare l'identità dell'utente autenticato corrente e le attestazioni in ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/claimsprincipal-current
ms.openlocfilehash: 4bcbaa1854016d7393d019a9c275bc8221974d7a
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84145616"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Esegui la migrazione da ClaimsPrincipal. Current

Nei progetti ASP.NET 4. x, era comune usare [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) per recuperare l'identità e le attestazioni dell'utente autenticato corrente. In ASP.NET Core questa proprietà non è più impostata. Il codice che dipende da esso deve essere aggiornato per ottenere l'identità dell'utente autenticato corrente tramite un mezzo diverso.

## <a name="context-specific-state-instead-of-static-state"></a>Stato specifico del contesto anziché stato statico

Quando si usa ASP.NET Core, i valori di `ClaimsPrincipal.Current` e `Thread.CurrentPrincipal` non sono impostati. Queste proprietà rappresentano entrambi lo stato statico, che ASP.NET Core in genere evita. Al contrario, ASP.NET Core usa l' [inserimento delle dipendenze](xref:fundamentals/dependency-injection) (di) per fornire dipendenze, ad esempio l'identità dell'utente corrente. Il recupero dell'identità dell'utente corrente da un è più testabile, perché le identità DI test possono essere facilmente inserite.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Recuperare l'utente corrente in un'app ASP.NET Core

Sono disponibili diverse opzioni per recuperare l'utente autenticato corrente `ClaimsPrincipal` in ASP.NET Core al posto di `ClaimsPrincipal.Current` :

* **ControllerBase. User**. I controller MVC possono accedere all'utente autenticato corrente con la relativa proprietà [utente](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) .
* **HttpContext. User**. I componenti con accesso alla corrente `HttpContext` (ad esempio, middleware) possono ottenere l'utente corrente `ClaimsPrincipal` da [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Passato dal chiamante**. Le librerie senza accesso all'oggetto corrente `HttpContext` vengono spesso chiamate da controller o componenti middleware e possono avere l'identità dell'utente corrente passata come argomento.
* **IHttpContextAccessor**. Il progetto di cui è in corso la migrazione a ASP.NET Core può essere troppo grande per passare facilmente l'identità dell'utente corrente a tutte le posizioni necessarie. In questi casi, è possibile usare [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) come soluzione alternativa. `IHttpContextAccessor`è in grado di accedere all'oggetto corrente, `HttpContext` se esistente. Se si usa l'inserimento DI dipendenze, vedere <xref:fundamentals/httpcontext> . Una soluzione a breve termine che consente di ottenere l'identità dell'utente corrente nel codice che non è ancora stata aggiornata per lavorare con l'architettura basata su DI ASP.NET Core è:

  * Rendere `IHttpContextAccessor` disponibile nel contenitore di inserimento delle dipendenze chiamando [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) in `Startup.ConfigureServices` .
  * Ottenere un'istanza di `IHttpContextAccessor` durante l'avvio e archiviarla in una variabile statica. L'istanza viene resa disponibile al codice recuperato in precedenza dall'utente corrente da una proprietà statica.
  * Recuperare l'utente corrente `ClaimsPrincipal` usando `HttpContextAccessor.HttpContext?.User` . Se questo codice viene usato al di fuori del contesto di una richiesta HTTP, `HttpContext` è null.

L'opzione finale, che usa un' `IHttpContextAccessor` istanza archiviata in una variabile statica, è contraria al principio ASP.NET Core di preferire le dipendenze inserite alle dipendenze statiche. Pianificare la fine `IHttpContextAccessor` del recupero delle istanze da di. Un helper statico può essere un Bridge utile, tuttavia, quando si esegue la migrazione di grandi app ASP.NET esistenti che usano `ClaimsPrincipal.Current` .
