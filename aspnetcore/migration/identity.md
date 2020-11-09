---
title: "Eseguire la migrazione dell'autenticazione e Identity a ASP.NET Core"
author: ardalis
description: Informazioni su come eseguire la migrazione dell'autenticazione e dell'identità da un progetto MVC ASP.NET a un progetto MVC ASP.NET Core.
ms.author: riande
ms.date: 3/22/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: migration/identity
ms.openlocfilehash: 8ceff0596c069d815c38b9bb526477a9d1430951
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060651"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core"></a><span data-ttu-id="1de1b-103">Eseguire la migrazione dell'autenticazione e Identity a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1de1b-103">Migrate Authentication and Identity to ASP.NET Core</span></span>

<span data-ttu-id="1de1b-104">Di [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="1de1b-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1de1b-105">Nell'articolo precedente è stata [eseguita la migrazione della configurazione da un progetto mvc ASP.NET a ASP.NET Core MVC](xref:migration/configuration).</span><span class="sxs-lookup"><span data-stu-id="1de1b-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="1de1b-106">Questo articolo illustra come eseguire la migrazione delle funzionalità di registrazione, accesso e gestione degli utenti.</span><span class="sxs-lookup"><span data-stu-id="1de1b-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-no-locidentity-and-membership"></a><span data-ttu-id="1de1b-107">Configurare Identity e appartenere</span><span class="sxs-lookup"><span data-stu-id="1de1b-107">Configure Identity and Membership</span></span>

<span data-ttu-id="1de1b-108">In ASP.NET MVC le funzionalità di autenticazione e identità vengono configurate usando ASP.NET Identity in *Startup.Auth.cs* e *Identity config.cs* , che si trova nella cartella *app_start* .</span><span class="sxs-lookup"><span data-stu-id="1de1b-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET Identity in *Startup.Auth.cs* and *IdentityConfig.cs* , located in the *App_Start* folder.</span></span> <span data-ttu-id="1de1b-109">In ASP.NET Core MVC queste funzionalità sono configurate in *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="1de1b-109">In ASP.NET Core MVC, these features are configured in *Startup.cs* .</span></span>

<span data-ttu-id="1de1b-110">Installare i pacchetti NuGet seguenti:</span><span class="sxs-lookup"><span data-stu-id="1de1b-110">Install the following NuGet packages:</span></span>

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.Cookies`
* `Microsoft.EntityFrameworkCore.SqlServer`

<span data-ttu-id="1de1b-111">In *Startup.cs* aggiornare il `Startup.ConfigureServices` metodo per usare Entity Framework e i Identity Servizi:</span><span class="sxs-lookup"><span data-stu-id="1de1b-111">In *Startup.cs* , update the `Startup.ConfigureServices` method to use Entity Framework and Identity services:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

<span data-ttu-id="1de1b-112">A questo punto, esistono due tipi a cui si fa riferimento nel codice precedente, di cui non è ancora stata eseguita la migrazione dal progetto MVC ASP.NET: `ApplicationDbContext` e `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="1de1b-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="1de1b-113">Creare una nuova cartella *Models* nel progetto ASP.NET Core e aggiungere due classi alla classe corrispondente a questi tipi.</span><span class="sxs-lookup"><span data-stu-id="1de1b-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="1de1b-114">Sono disponibili le versioni ASP.NET MVC di queste classi in */Models/ Identity Models.cs* , ma si userà un file per classe nel progetto migrato, perché questo è più chiaro.</span><span class="sxs-lookup"><span data-stu-id="1de1b-114">You will find the ASP.NET MVC versions of these classes in */Models/IdentityModels.cs* , but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="1de1b-115">*ApplicationUser.cs* :</span><span class="sxs-lookup"><span data-stu-id="1de1b-115">*ApplicationUser.cs* :</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

<span data-ttu-id="1de1b-116">*ApplicationDbContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="1de1b-116">*ApplicationDbContext.cs* :</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

<span data-ttu-id="1de1b-117">Il progetto Web di avvio ASP.NET Core MVC non include molte personalizzazioni degli utenti o `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="1de1b-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="1de1b-118">Quando si esegue la migrazione di un'app reale, è anche necessario eseguire la migrazione di tutti i metodi e le proprietà personalizzate dell'utente e delle classi dell'app, oltre alle `DbContext` altre classi di modelli usate dall'app.</span><span class="sxs-lookup"><span data-stu-id="1de1b-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="1de1b-119">Se, ad esempio, `DbContext` è presente un oggetto `DbSet<Album>` , è necessario eseguire la migrazione della `Album` classe.</span><span class="sxs-lookup"><span data-stu-id="1de1b-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="1de1b-120">Con questi file, è possibile compilare il file *Startup.cs* aggiornando le relative `using` istruzioni:</span><span class="sxs-lookup"><span data-stu-id="1de1b-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="1de1b-121">L'app è ora pronta per supportare l'autenticazione e i Identity servizi.</span><span class="sxs-lookup"><span data-stu-id="1de1b-121">Our app is now ready to support authentication and Identity services.</span></span> <span data-ttu-id="1de1b-122">È sufficiente che queste funzionalità siano esposte agli utenti.</span><span class="sxs-lookup"><span data-stu-id="1de1b-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="1de1b-123">Eseguire la migrazione della logica di registrazione e accesso</span><span class="sxs-lookup"><span data-stu-id="1de1b-123">Migrate registration and login logic</span></span>

<span data-ttu-id="1de1b-124">Con Identity i servizi configurati per l'app e l'accesso ai dati configurati con Entity Framework e SQL Server, è possibile aggiungere il supporto per la registrazione e l'accesso all'app.</span><span class="sxs-lookup"><span data-stu-id="1de1b-124">With Identity services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="1de1b-125">Si ricordi che [in precedenza nel processo di migrazione](xref:migration/mvc#migrate-the-layout-file) è stato impostato come commento un riferimento a *_LoginPartial* in *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1de1b-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml* .</span></span> <span data-ttu-id="1de1b-126">A questo punto è possibile tornare a tale codice, rimuovere il commento e aggiungere i controller e le visualizzazioni necessari per supportare la funzionalità di accesso.</span><span class="sxs-lookup"><span data-stu-id="1de1b-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="1de1b-127">Rimuovere il commento dalla `@Html.Partial` riga in *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1de1b-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml* :</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="1de1b-128">A questo punto, aggiungere una nuova Razor vista denominata *_LoginPartial* alla cartella *Views/Shared* :</span><span class="sxs-lookup"><span data-stu-id="1de1b-128">Now, add a new Razor view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="1de1b-129">Aggiornare *_LoginPartial. cshtml* con il codice seguente (sostituire tutto il contenuto):</span><span class="sxs-lookup"><span data-stu-id="1de1b-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

<span data-ttu-id="1de1b-130">A questo punto, dovrebbe essere possibile aggiornare il sito nel browser.</span><span class="sxs-lookup"><span data-stu-id="1de1b-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="1de1b-131">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="1de1b-131">Summary</span></span>

<span data-ttu-id="1de1b-132">ASP.NET Core introduce le modifiche apportate alle Identity funzionalità ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1de1b-132">ASP.NET Core introduces changes to the ASP.NET Identity features.</span></span> <span data-ttu-id="1de1b-133">In questo articolo è stato illustrato come eseguire la migrazione delle funzionalità di autenticazione e gestione utenti di ASP.NET Identity in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1de1b-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET Identity to ASP.NET Core.</span></span>
