---
title: Esempio di ASP.NET Core cookie navigava sullostesso sito 2,1 MVC
author: rick-anderson
description: Esempio di ASP.NET Core cookie navigava sullostesso sito 2,1 MVC
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/samesite/mvc21
ms.openlocfilehash: 6a53c0d3c0a314c4137f071cf50062182b654658
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777306"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a><span data-ttu-id="3f8c1-103">Esempio di ASP.NET Core cookie navigava sullostesso sito 2,1 MVC</span><span class="sxs-lookup"><span data-stu-id="3f8c1-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="3f8c1-104">ASP.NET Core 2,1 dispone del supporto incorporato per l'attributo [navigava sullostesso sito](https://www.owasp.org/index.php/SameSite) , ma è stato scritto nello standard originale.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="3f8c1-105">Il [comportamento con patch](https://github.com/dotnet/aspnetcore/issues/8212) ha modificato il significato `SameSite.None` di per emettere l'attributo navigava sullostesso sito con un valore `None`di, anziché non creare il valore.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="3f8c1-106">Se non si desidera creare il valore, è possibile impostare la `SameSite` proprietà su un cookie su-1.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="3f8c1-107">Scrittura dell'attributo navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="3f8c1-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="3f8c1-108">Di seguito è riportato un esempio di come scrivere un attributo navigava sullostesso sito in un cookie:</span><span class="sxs-lookup"><span data-stu-id="3f8c1-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a><span data-ttu-id="3f8c1-109">Impostazione dei cookie di stato della sessione e dell'autenticazione dei cookie</span><span class="sxs-lookup"><span data-stu-id="3f8c1-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="3f8c1-110">Autenticazione dei cookie, stato sessione e [vari altri componenti](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) impostare le opzioni navigava sullostesso sito tramite opzioni cookie, ad esempio</span><span class="sxs-lookup"><span data-stu-id="3f8c1-110">Cookie authentication, session state and [various other components](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

<span data-ttu-id="3f8c1-111">Nel codice precedente, sia l'autenticazione dei cookie che lo stato della sessione impostano il relativo attributo navigava sullostesso sito su `None`, `None` creando l'attributo con un valore e impostando anche l'attributo Secure su true.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="3f8c1-112">Eseguire l'esempio</span><span class="sxs-lookup"><span data-stu-id="3f8c1-112">Run the sample</span></span>

<span data-ttu-id="3f8c1-113">Se si esegue il [progetto di esempio](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), caricare il debugger del browser nella pagina iniziale e usarlo per visualizzare la raccolta di cookie per il sito.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="3f8c1-114">A tale scopo, in Edge e Chrome `F12` premere quindi selezionare `Application` la scheda e fare clic sull'URL del `Cookies` sito sotto l' `Storage` opzione nella sezione.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![Elenco dei cookie del debugger del browser](BrowserDebugger.png)

<span data-ttu-id="3f8c1-116">È possibile osservare dall'immagine precedente che il cookie creato dall'esempio quando si fa clic sul pulsante "crea cookie navigava sullostesso sito" ha un valore di `Lax`attributo navigava sullostesso sito, che corrisponde al valore impostato nel codice di [esempio](#sampleCode).</span><span class="sxs-lookup"><span data-stu-id="3f8c1-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-cookies"></a><a name="interception"></a><span data-ttu-id="3f8c1-117">Intercettazione di cookie</span><span class="sxs-lookup"><span data-stu-id="3f8c1-117">Intercepting cookies</span></span>

<span data-ttu-id="3f8c1-118">Per intercettare i cookie, per modificare il valore None in base al relativo supporto nell'agente browser dell'utente, è necessario usare `CookiePolicy` il middleware.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="3f8c1-119">Questa deve essere inserita nella pipeline di richieste HTTP **prima** di tutti i componenti che scrivono cookie e `ConfigureServices()`configurati in.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="3f8c1-120">Per inserirlo nell'uso `app.UseCookiePolicy()` della pipeline nel `Configure(IApplicationBuilder, IHostingEnvironment)` metodo in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="3f8c1-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="3f8c1-121">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="3f8c1-121">For example:</span></span>

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="3f8c1-122">Quindi, nel `ConfigureServices(IServiceCollection services)` configurare i criteri dei cookie per chiamare una classe helper quando i cookie vengono aggiunti o eliminati.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="3f8c1-123">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="3f8c1-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="3f8c1-124">Funzione `CheckSameSite(HttpContext, CookieOptions)`Helper:</span><span class="sxs-lookup"><span data-stu-id="3f8c1-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="3f8c1-125">Viene chiamato quando i cookie vengono aggiunti alla richiesta o eliminati dalla richiesta.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="3f8c1-126">Verifica se la `SameSite` proprietà è impostata su `None`.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="3f8c1-127">Se `SameSite` è impostato su `None` e l'agente utente corrente non supporta il valore dell'attributo None.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="3f8c1-128">Il controllo viene eseguito usando la classe [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :</span><span class="sxs-lookup"><span data-stu-id="3f8c1-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="3f8c1-129">Imposta `SameSite` per non creare il valore impostando la proprietà su`(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="3f8c1-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="3f8c1-130">.NET Framework di destinazione</span><span class="sxs-lookup"><span data-stu-id="3f8c1-130">Targeting .NET Framework</span></span>

<span data-ttu-id="3f8c1-131">ASP.NET Core e System. Web (ASP.NET classico) hanno implementazioni indipendenti di navigava sullostesso sito.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="3f8c1-132">Le patch di navigava sullostesso sito KB per .NET Framework non sono necessarie se si usa ASP.NET Core e non viene applicato il requisito di versione minima del Framework System. Web navigava sullostesso sito (.NET 4.7.2) a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="3f8c1-133">ASP.NET Core su .NET richiede l'aggiornamento delle dipendenze dei pacchetti NuGet per ottenere le correzioni appropriate.</span><span class="sxs-lookup"><span data-stu-id="3f8c1-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="3f8c1-134">Per ottenere le modifiche ASP.NET Core per .NET Framework assicurarsi di disporre di un riferimento diretto alle versioni e ai pacchetti con patch (2.1.14 o versioni successive 2,1).</span><span class="sxs-lookup"><span data-stu-id="3f8c1-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="3f8c1-135">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="3f8c1-135">More Information</span></span>
 
<span data-ttu-id="3f8c1-136">[Aggiornamenti](https://www.chromium.org/updates/same-site)
di Chrome[ASP.NET Core documentazione](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
di navigava sullostesso sito[ASP.NET Core 2,1 annuncio di modifica navigava sullostesso sito](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="3f8c1-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>