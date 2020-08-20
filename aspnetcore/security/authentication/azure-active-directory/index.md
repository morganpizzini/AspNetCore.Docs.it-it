---
title: Piattaforma di identità Microsoft e Azure Active Directory con ASP.NET Core
author: rick-anderson
description: Argomenti relativi all'autenticazione con la piattaforma di identità Microsoft Azure Active Directory per le app Web e le API nel ASP.NET Core.
ms.author: riande
ms.date: 01/21/2020
ms.custom: mvc, seodec18
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
uid: security/authentication/azure-active-directory/index
ms.openlocfilehash: b807681e00b196449c74dbe4240e9d6996af51fc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633084"
---
# <a name="azure-active-directory-with-aspnet-core"></a><span data-ttu-id="d5546-103">Azure Active Directory con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5546-103">Azure Active Directory with ASP.NET Core</span></span>

<span data-ttu-id="d5546-104">Queste esercitazioni ed esempi illustrano l'autenticazione in ASP.NET Core usando la piattaforma di identità Microsoft e Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="d5546-104">These tutorials and samples demonstrate authentication in ASP.NET Core using Microsoft identity platform and Azure Active Directory.</span></span> <span data-ttu-id="d5546-105">Per ulteriori esercitazioni ed esempi sull'utilizzo di ASP.NET Core con Azure AD, vedere la pagina relativa alla [piattaforma delle identità Microsoft](/azure/active-directory/develop/).</span><span class="sxs-lookup"><span data-stu-id="d5546-105">For additional tutorials and samples using ASP.NET Core with Azure AD, see [Microsoft identity platform](/azure/active-directory/develop/).</span></span>

## <a name="application-scenarios"></a><span data-ttu-id="d5546-106">Scenari di applicazione</span><span class="sxs-lookup"><span data-stu-id="d5546-106">Application Scenarios</span></span>

* [<span data-ttu-id="d5546-107">Avvio rapido: Aggiungere l'accesso con Microsoft a un'app Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5546-107">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="d5546-108">App Web che offre l'accesso agli utenti</span><span class="sxs-lookup"><span data-stu-id="d5546-108">Web app that signs in users</span></span>](/azure/active-directory/develop/scenario-web-app-sign-user-overview?tabs=aspnetcore)
* [<span data-ttu-id="d5546-109">App Web che chiama le API Web</span><span class="sxs-lookup"><span data-stu-id="d5546-109">Web app that calls web APIs</span></span>](/azure/active-directory/develop/scenario-web-app-call-api-overview)
* [<span data-ttu-id="d5546-110">API Web protetta</span><span class="sxs-lookup"><span data-stu-id="d5546-110">Protected web API</span></span>](/azure/active-directory/develop/scenario-protected-web-api-overview)
* [<span data-ttu-id="d5546-111">App Web che chiama altre API Web</span><span class="sxs-lookup"><span data-stu-id="d5546-111">Web API that calls other web APIs</span></span>](/azure/active-directory/develop/scenario-web-api-call-api-overview)
* [<span data-ttu-id="d5546-112">App Web che consente agli utenti di accedere con Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="d5546-112">Web app that signs in users with Azure AD B2C</span></span>](xref:security/authentication/azure-ad-b2c)

## <a name="samples"></a><span data-ttu-id="d5546-113">Esempi</span><span class="sxs-lookup"><span data-stu-id="d5546-113">Samples</span></span>

* <span data-ttu-id="d5546-114">[Abilitare l'app ASP.NET Core per gli utenti di accesso e chiamare le API Web usando Azure ad V2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span><span class="sxs-lookup"><span data-stu-id="d5546-114">[Enable your ASP.NET Core app to sign-in users and call web APIs using Azure AD V2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span></span> 
  * <span data-ttu-id="d5546-115">Vedere [questo video associato](https://channel9.msdn.com/Events/Build/2018/THR5001)</span><span class="sxs-lookup"><span data-stu-id="d5546-115">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5001)</span></span>

* <span data-ttu-id="d5546-116">[Chiamata di un'API Web ASP.NET Core 2,0 da un'applicazione WPF con Azure ad V2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span><span class="sxs-lookup"><span data-stu-id="d5546-116">[Calling an ASP.NET Core 2.0 Web API from a WPF application using Azure AD V2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span></span> 
  * <span data-ttu-id="d5546-117">Vedere [questo video associato](https://channel9.msdn.com/Events/Build/2018/THR5000)</span><span class="sxs-lookup"><span data-stu-id="d5546-117">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5000)</span></span>

* [<span data-ttu-id="d5546-118">Un'app API Web ASP.NET Core con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="d5546-118">An ASP.NET Core web API with Azure AD B2C</span></span>](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/)
