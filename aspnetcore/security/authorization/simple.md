---
<span data-ttu-id="02bf6-101">title: autorizzazione semplice in ASP.NET Core autore: Rick-Anderson Description: informazioni su come usare l'attributo di autorizzazione per limitare l'accesso alle azioni e ai controller ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="02bf6-101">title: Simple authorization in ASP.NET Core author: rick-anderson description: Learn how to use the Authorize attribute to restrict access to ASP.NET Core controllers and actions.</span></span>
<span data-ttu-id="02bf6-102">ms. Author: Riande ms. Date: 10/14/2016 no-loc:</span><span class="sxs-lookup"><span data-stu-id="02bf6-102">ms.author: riande ms.date: 10/14/2016 no-loc:</span></span>
- <span data-ttu-id="02bf6-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02bf6-103">'Blazor'</span></span>
- <span data-ttu-id="02bf6-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02bf6-104">'Identity'</span></span>
- <span data-ttu-id="02bf6-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02bf6-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="02bf6-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02bf6-106">'Razor'</span></span>
- <span data-ttu-id="02bf6-107">' SignalR ' UID: sicurezza/autorizzazione/semplice</span><span class="sxs-lookup"><span data-stu-id="02bf6-107">'SignalR' uid: security/authorization/simple</span></span>

---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="02bf6-108">Autorizzazione semplice in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="02bf6-108">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="02bf6-109">L'autorizzazione in ASP.NET Core viene controllata con <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> e i vari parametri.</span><span class="sxs-lookup"><span data-stu-id="02bf6-109">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="02bf6-110">Nella sua forma più semplice, applicando l' `[Authorize]` attributo a un controller, a un'azione o a una Razor pagina, è possibile limitare l'accesso a tale componente a qualsiasi utente autenticato.</span><span class="sxs-lookup"><span data-stu-id="02bf6-110">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="02bf6-111">Il codice seguente, ad esempio, limita l'accesso a `AccountController` a qualsiasi utente autenticato.</span><span class="sxs-lookup"><span data-stu-id="02bf6-111">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="02bf6-112">Se si desidera applicare l'autorizzazione a un'azione anziché al controller, applicare l' `AuthorizeAttribute` attributo all'azione stessa:</span><span class="sxs-lookup"><span data-stu-id="02bf6-112">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="02bf6-113">A questo punto, solo gli utenti autenticati possono accedere alla `Logout` funzione.</span><span class="sxs-lookup"><span data-stu-id="02bf6-113">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="02bf6-114">È anche possibile usare l' `AllowAnonymous` attributo per consentire l'accesso da parte di utenti non autenticati a singole azioni.</span><span class="sxs-lookup"><span data-stu-id="02bf6-114">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="02bf6-115">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="02bf6-115">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="02bf6-116">Ciò consente solo agli utenti autenticati di `AccountController` , ad eccezione dell' `Login` azione, accessibile da parte di tutti, indipendentemente dallo stato autenticato o non autenticato/anonimo.</span><span class="sxs-lookup"><span data-stu-id="02bf6-116">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="02bf6-117">`[AllowAnonymous]`Ignora tutte le istruzioni di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="02bf6-117">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="02bf6-118">Se si combinano `[AllowAnonymous]` e qualsiasi `[Authorize]` attributo, gli `[Authorize]` attributi vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="02bf6-118">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="02bf6-119">Se ad esempio si applica `[AllowAnonymous]` a livello di controller, tutti `[Authorize]` gli attributi sullo stesso controller (o su qualsiasi azione al suo interno) vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="02bf6-119">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
