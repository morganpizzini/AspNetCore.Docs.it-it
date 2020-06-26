---
title: Autorizzazione basata sulle attestazioni in ASP.NET Core
author: rick-anderson
description: Informazioni su come aggiungere i controlli delle attestazioni per l'autorizzazione in un'app ASP.NET Core.
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
uid: security/authorization/claims
ms.openlocfilehash: 404e26f0fb5e71dbc22b1c08a2f8caf8461ad7e1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406381"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="35673-103">Autorizzazione basata sulle attestazioni in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35673-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="35673-104">Quando viene creata un'identità, è possibile che venga assegnata una o più attestazioni rilasciate da un'entità attendibile.</span><span class="sxs-lookup"><span data-stu-id="35673-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="35673-105">Un'attestazione è una coppia nome-valore che rappresenta il tipo di oggetto, non ciò che può fare l'oggetto.</span><span class="sxs-lookup"><span data-stu-id="35673-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="35673-106">È possibile, ad esempio, che si disponga di una licenza di un driver, emessa da un'autorità di licenza di guida locale.</span><span class="sxs-lookup"><span data-stu-id="35673-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="35673-107">La licenza del driver ha la data di nascita.</span><span class="sxs-lookup"><span data-stu-id="35673-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="35673-108">In questo caso il nome dell'attestazione è `DateOfBirth` , il valore dell'attestazione è la data di nascita, ad esempio, `8th June 1970` e l'emittente è l'autorità di licenza di guida.</span><span class="sxs-lookup"><span data-stu-id="35673-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="35673-109">L'autorizzazione basata sulle attestazioni, alla sua più semplice, controlla il valore di un'attestazione e consente l'accesso a una risorsa in base a tale valore.</span><span class="sxs-lookup"><span data-stu-id="35673-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="35673-110">Se ad esempio si vuole accedere a un night club, il processo di autorizzazione potrebbe essere:</span><span class="sxs-lookup"><span data-stu-id="35673-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="35673-111">Il responsabile della sicurezza di sportello valuterebbe il valore della data di attestazione di nascita e se considera attendibile l'autorità emittente (l'autorità di certificazione di guida) prima di concedere l'accesso.</span><span class="sxs-lookup"><span data-stu-id="35673-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="35673-112">Un'identità può contenere più attestazioni con più valori e può contenere più attestazioni dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="35673-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="35673-113">Aggiunta di controlli delle attestazioni</span><span class="sxs-lookup"><span data-stu-id="35673-113">Adding claims checks</span></span>

<span data-ttu-id="35673-114">I controlli delle autorizzazioni basati sulle attestazioni sono dichiarativi. lo sviluppatore li incorpora all'interno del codice, a fronte di un controller o di un'azione all'interno di un controller, specificando attestazioni che l'utente corrente deve possedere e, facoltativamente, il valore che l'attestazione deve tenere per accedere alla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="35673-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="35673-115">I requisiti di attestazione sono basati su criteri, lo sviluppatore deve compilare e registrare un criterio che esprime i requisiti di attestazione.</span><span class="sxs-lookup"><span data-stu-id="35673-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="35673-116">Il tipo più semplice di criteri di attestazione cerca la presenza di un'attestazione e non verifica il valore.</span><span class="sxs-lookup"><span data-stu-id="35673-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="35673-117">Prima di tutto è necessario compilare e registrare i criteri.</span><span class="sxs-lookup"><span data-stu-id="35673-117">First you need to build and register the policy.</span></span> <span data-ttu-id="35673-118">Questo avviene come parte della configurazione del servizio di autorizzazione, che in genere fa parte del `ConfigureServices()` file *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="35673-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

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

<span data-ttu-id="35673-119">In questo caso i `EmployeeOnly` criteri verificano la presenza di un' `EmployeeNumber` attestazione sull'identità corrente.</span><span class="sxs-lookup"><span data-stu-id="35673-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="35673-120">Applicare quindi il criterio usando la `Policy` proprietà nell' `AuthorizeAttribute` attributo per specificare il nome del criterio;</span><span class="sxs-lookup"><span data-stu-id="35673-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="35673-121">L' `AuthorizeAttribute` attributo può essere applicato a un intero controller, in questo caso solo le identità che corrispondono ai criteri saranno autorizzate ad accedere a qualsiasi azione nel controller.</span><span class="sxs-lookup"><span data-stu-id="35673-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="35673-122">Se si dispone di un controller protetto dall' `AuthorizeAttribute` attributo, ma si desidera consentire l'accesso anonimo a determinate azioni, si applica l' `AllowAnonymousAttribute` attributo.</span><span class="sxs-lookup"><span data-stu-id="35673-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

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

<span data-ttu-id="35673-123">Per la maggior parte delle attestazioni viene aggiunto un valore.</span><span class="sxs-lookup"><span data-stu-id="35673-123">Most claims come with a value.</span></span> <span data-ttu-id="35673-124">È possibile specificare un elenco di valori consentiti durante la creazione del criterio.</span><span class="sxs-lookup"><span data-stu-id="35673-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="35673-125">L'esempio seguente ha esito positivo solo per i dipendenti il cui numero di dipendente è 1, 2, 3, 4 o 5.</span><span class="sxs-lookup"><span data-stu-id="35673-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

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
### <a name="add-a-generic-claim-check"></a><span data-ttu-id="35673-126">Aggiungere un controllo di attestazione generico</span><span class="sxs-lookup"><span data-stu-id="35673-126">Add a generic claim check</span></span>

<span data-ttu-id="35673-127">Se il valore dell'attestazione non è un singolo valore o se è richiesta una trasformazione, utilizzare [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span><span class="sxs-lookup"><span data-stu-id="35673-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="35673-128">Per ulteriori informazioni, vedere [utilizzare una funzione Func per soddisfare i criteri](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span><span class="sxs-lookup"><span data-stu-id="35673-128">For more information, see [Use a func to fulfill a policy](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="35673-129">Valutazione di più criteri</span><span class="sxs-lookup"><span data-stu-id="35673-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="35673-130">Se si applicano più criteri a un controller o a un'azione, tutti i criteri devono essere superati prima che venga concesso l'accesso.</span><span class="sxs-lookup"><span data-stu-id="35673-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="35673-131">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="35673-131">For example:</span></span>

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

<span data-ttu-id="35673-132">Nell'esempio precedente qualsiasi identità che soddisfa il `EmployeeOnly` criterio può accedere all' `Payslip` azione perché i criteri vengono applicati nel controller.</span><span class="sxs-lookup"><span data-stu-id="35673-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="35673-133">Tuttavia, per chiamare l' `UpdateSalary` azione è necessario che l'identità soddisfi *sia* i `EmployeeOnly` criteri che i `HumanResources` criteri.</span><span class="sxs-lookup"><span data-stu-id="35673-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="35673-134">Se si desiderano criteri più complessi, ad esempio l'acquisizione di una data di attestazione di nascita, il calcolo di un periodo di tempo da esso, il controllo dell'età è 21 o precedente, è necessario scrivere [gestori di criteri personalizzati](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="35673-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
