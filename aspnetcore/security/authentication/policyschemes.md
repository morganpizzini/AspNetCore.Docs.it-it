---
title: Schemi di criteri in ASP.NET Core
author: rick-anderson
description: Gli schemi dei criteri di autenticazione rendono più semplice avere un unico schema di autenticazione logica
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 60ac9914ef811a705c61ab3b2bec61643acc6ec0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634982"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="ddb37-103">Schemi di criteri in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ddb37-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="ddb37-104">Gli schemi dei criteri di autenticazione rendono più semplice disporre di un singolo schema di autenticazione logica che potenzialmente utilizza diversi approcci.</span><span class="sxs-lookup"><span data-stu-id="ddb37-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="ddb37-105">Ad esempio, uno schema di criteri può utilizzare l'autenticazione Google per le richieste e cookie l'autenticazione per tutti gli altri elementi.</span><span class="sxs-lookup"><span data-stu-id="ddb37-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="ddb37-106">Gli schemi dei criteri di autenticazione lo rendono:</span><span class="sxs-lookup"><span data-stu-id="ddb37-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="ddb37-107">Facile da inviare qualsiasi azione di autenticazione a un altro schema.</span><span class="sxs-lookup"><span data-stu-id="ddb37-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="ddb37-108">In avanti in modo dinamico in base alla richiesta.</span><span class="sxs-lookup"><span data-stu-id="ddb37-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="ddb37-109">Tutti gli schemi di autenticazione che usano Derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> e il [AuthenticationHandler \<TOptions> ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)associato:</span><span class="sxs-lookup"><span data-stu-id="ddb37-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="ddb37-110">Gli schemi di criteri sono automaticamente in ASP.NET Core 2,1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="ddb37-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="ddb37-111">Può essere abilitato tramite la configurazione delle opzioni dello schema.</span><span class="sxs-lookup"><span data-stu-id="ddb37-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="ddb37-112">Esempi</span><span class="sxs-lookup"><span data-stu-id="ddb37-112">Examples</span></span>

<span data-ttu-id="ddb37-113">Nell'esempio seguente viene illustrato uno schema di livello superiore che combina schemi di livello inferiore.</span><span class="sxs-lookup"><span data-stu-id="ddb37-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="ddb37-114">Google Authentication viene usato per le richieste e cookie l'autenticazione viene usata per tutte le altre operazioni:</span><span class="sxs-lookup"><span data-stu-id="ddb37-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="ddb37-115">Nell'esempio seguente viene abilitata la selezione dinamica degli schemi in base alle singole richieste.</span><span class="sxs-lookup"><span data-stu-id="ddb37-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="ddb37-116">Ovvero come combinare cookie s e l'autenticazione API:</span><span class="sxs-lookup"><span data-stu-id="ddb37-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
