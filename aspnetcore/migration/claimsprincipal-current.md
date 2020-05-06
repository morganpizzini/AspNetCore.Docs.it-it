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
ms.openlocfilehash: 5f6b5b960eacf176088d9fc60f9ba16014e613fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776090"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Esegui la migrazione da ClaimsPrincipal. Current

Nei progetti ASP.NET 4. x, era comune usare [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) per recuperare l'identità e le attestazioni dell'utente autenticato corrente. In ASP.NET Core questa proprietà non è più impostata. Il codice che dipende da esso deve essere aggiornato per ottenere l'identità dell'utente autenticato corrente tramite un mezzo diverso.

## <a name="context-specific-data-instead-of-static-data"></a>Dati specifici del contesto anziché dati statici

Quando si usa ASP.NET Core, i valori di `ClaimsPrincipal.Current` e `Thread.CurrentPrincipal` non sono impostati. Queste proprietà rappresentano entrambi lo stato statico, che ASP.NET Core in genere evita. L'architettura di ASP.NET Core è invece recuperare le dipendenze (ad esempio l'identità dell'utente corrente) da raccolte di servizi specifiche del contesto (usando il modello di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) ). Il thread `Thread.CurrentPrincipal` è statico, quindi non può rendere permanente le modifiche in alcuni scenari asincroni (e `ClaimsPrincipal.Current` chiama `Thread.CurrentPrincipal` per impostazione predefinita).

Per comprendere i tipi di problemi che i membri statici del thread possono causare in scenari asincroni, si consideri il frammento di codice seguente:

```csharp
// Create a ClaimsPrincipal and set Thread.CurrentPrincipal
var identity = new ClaimsIdentity();
identity.AddClaim(new Claim(ClaimTypes.Name, "User1"));
Thread.CurrentPrincipal = new ClaimsPrincipal(identity);

// Check the current user
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");

// For the method to complete asynchronously
await Task.Yield();

// Check the current user after
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");
```

Il codice di esempio precedente `Thread.CurrentPrincipal` imposta e controlla il relativo valore prima e dopo l'attesa di una chiamata asincrona. `Thread.CurrentPrincipal`è specifico per il *thread* su cui è impostato e il metodo può riprendere l'esecuzione in un thread diverso dopo l'attesa. Di conseguenza `Thread.CurrentPrincipal` , è presente quando viene verificato per la prima volta ma è null dopo `await Task.Yield()`la chiamata a.

Ottenere l'identità dell'utente corrente dalla raccolta DI servizi dell'app è più testabile, perché le identità di test possono essere facilmente inserite.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Recuperare l'utente corrente in un'app ASP.NET Core

Sono disponibili diverse opzioni per recuperare l'utente autenticato corrente `ClaimsPrincipal` in ASP.NET Core al posto di: `ClaimsPrincipal.Current`

* **ControllerBase. User**. I controller MVC possono accedere all'utente autenticato corrente con la relativa proprietà [utente](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) .
* **HttpContext. User**. I componenti con accesso alla corrente `HttpContext` (ad esempio, middleware) possono ottenere l'utente corrente `ClaimsPrincipal` da [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Passato dal chiamante**. Le librerie senza accesso all'oggetto `HttpContext` corrente vengono spesso chiamate da controller o componenti middleware e possono avere l'identità dell'utente corrente passata come argomento.
* **IHttpContextAccessor**. Il progetto di cui è in corso la migrazione a ASP.NET Core può essere troppo grande per passare facilmente l'identità dell'utente corrente a tutte le posizioni necessarie. In questi casi, è possibile usare [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) come soluzione alternativa. `IHttpContextAccessor`è in grado di accedere all' `HttpContext` oggetto corrente, se esistente. Se si usa l'inserimento DI dipendenze, vedere <xref:fundamentals/httpcontext>. Una soluzione a breve termine che consente di ottenere l'identità dell'utente corrente nel codice che non è ancora stata aggiornata per lavorare con l'architettura basata su DI ASP.NET Core è:

  * Rendere `IHttpContextAccessor` disponibile nel contenitore di inserimento delle dipendenze chiamando `Startup.ConfigureServices` [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) in.
  * Ottenere un'istanza di `IHttpContextAccessor` durante l'avvio e archiviarla in una variabile statica. L'istanza viene resa disponibile al codice recuperato in precedenza dall'utente corrente da una proprietà statica.
  * Recuperare l'utente corrente `ClaimsPrincipal` usando. `HttpContextAccessor.HttpContext?.User` Se questo codice viene usato al `HttpContext` di fuori del contesto di una richiesta HTTP, è null.

L'opzione finale, che usa `IHttpContextAccessor` un'istanza archiviata in una variabile statica, è contraria a ASP.NET Core principi (che preferiscono le dipendenze inserite a dipendenze statiche). Pianificare la fine del `IHttpContextAccessor` recupero delle istanze dall'inserimento delle dipendenze. Un helper statico può essere un Bridge utile, tuttavia, quando si esegue la migrazione di grandi app ASP.NET esistenti che `ClaimsPrincipal.Current`in precedenza usavano.
