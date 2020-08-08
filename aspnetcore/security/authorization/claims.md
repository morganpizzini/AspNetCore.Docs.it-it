---
title: Autorizzazione basata sulle attestazioni in ASP.NET Core
author: rick-anderson
description: Informazioni su come aggiungere i controlli delle attestazioni per l'autorizzazione in un'app ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/claims
ms.openlocfilehash: 639cacbab2688adfe769ef2c6954ea877cf0c66a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022355"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>Autorizzazione basata sulle attestazioni in ASP.NET Core

<a name="security-authorization-claims-based"></a>

Quando viene creata un'identità, è possibile che venga assegnata una o più attestazioni rilasciate da un'entità attendibile. Un'attestazione è una coppia nome-valore che rappresenta il tipo di oggetto, non ciò che può fare l'oggetto. È possibile, ad esempio, che si disponga di una licenza di un driver, emessa da un'autorità di licenza di guida locale. La licenza del driver ha la data di nascita. In questo caso il nome dell'attestazione è `DateOfBirth` , il valore dell'attestazione è la data di nascita, ad esempio, `8th June 1970` e l'emittente è l'autorità di licenza di guida. L'autorizzazione basata sulle attestazioni, alla sua più semplice, controlla il valore di un'attestazione e consente l'accesso a una risorsa in base a tale valore. Se ad esempio si vuole accedere a un night club, il processo di autorizzazione potrebbe essere:

Il responsabile della sicurezza di sportello valuterebbe il valore della data di attestazione di nascita e se considera attendibile l'autorità emittente (l'autorità di certificazione di guida) prima di concedere l'accesso.

Un'identità può contenere più attestazioni con più valori e può contenere più attestazioni dello stesso tipo.

## <a name="adding-claims-checks"></a>Aggiunta di controlli delle attestazioni

I controlli delle autorizzazioni basati sulle attestazioni sono dichiarativi. lo sviluppatore li incorpora all'interno del codice, a fronte di un controller o di un'azione all'interno di un controller, specificando attestazioni che l'utente corrente deve possedere e, facoltativamente, il valore che l'attestazione deve tenere per accedere alla risorsa richiesta. I requisiti di attestazione sono basati su criteri, lo sviluppatore deve compilare e registrare un criterio che esprime i requisiti di attestazione.

Il tipo più semplice di criteri di attestazione cerca la presenza di un'attestazione e non verifica il valore.

Prima di tutto è necessario compilare e registrare i criteri. Questo avviene come parte della configurazione del servizio di autorizzazione, che in genere fa parte del `ConfigureServices()` file *Startup.cs* .

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

In questo caso i `EmployeeOnly` criteri verificano la presenza di un' `EmployeeNumber` attestazione sull'identità corrente.

Applicare quindi il criterio usando la `Policy` proprietà nell' `AuthorizeAttribute` attributo per specificare il nome del criterio;

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

L' `AuthorizeAttribute` attributo può essere applicato a un intero controller, in questo caso solo le identità che corrispondono ai criteri saranno autorizzate ad accedere a qualsiasi azione nel controller.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

Se si dispone di un controller protetto dall' `AuthorizeAttribute` attributo, ma si desidera consentire l'accesso anonimo a determinate azioni, si applica l' `AllowAnonymousAttribute` attributo.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

Per la maggior parte delle attestazioni viene aggiunto un valore. È possibile specificare un elenco di valori consentiti durante la creazione del criterio. L'esempio seguente ha esito positivo solo per i dipendenti il cui numero di dipendente è 1, 2, 3, 4 o 5.

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a>Aggiungere un controllo di attestazione generico

Se il valore dell'attestazione non è un singolo valore o se è richiesta una trasformazione, utilizzare [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion). Per ulteriori informazioni, vedere [utilizzare una funzione Func per soddisfare i criteri](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).

## <a name="multiple-policy-evaluation"></a>Valutazione di più criteri

Se si applicano più criteri a un controller o a un'azione, tutti i criteri devono essere superati prima che venga concesso l'accesso. Ad esempio:

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

Nell'esempio precedente qualsiasi identità che soddisfa il `EmployeeOnly` criterio può accedere all' `Payslip` azione perché i criteri vengono applicati nel controller. Tuttavia, per chiamare l' `UpdateSalary` azione è necessario che l'identità soddisfi *sia* i `EmployeeOnly` criteri che i `HumanResources` criteri.

Se si desiderano criteri più complessi, ad esempio l'acquisizione di una data di attestazione di nascita, il calcolo di un periodo di tempo da esso, il controllo dell'età è 21 o precedente, è necessario scrivere [gestori di criteri personalizzati](xref:security/authorization/policies).
