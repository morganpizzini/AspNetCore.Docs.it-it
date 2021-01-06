---
title: Novità di ASP.NET Core 1.1
author: rick-anderson
description: Informazioni sulle nuove funzionalità in ASP.NET Core 1.1.
ms.author: riande
ms.date: 12/18/2018
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
uid: aspnetcore-1.1
ms.openlocfilehash: cc891280a6314dbc4c0838d5b4a8d2a20698eab6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059741"
---
# <a name="whats-new-in-aspnet-core-11"></a><span data-ttu-id="b70aa-103">Novità di ASP.NET Core 1.1</span><span class="sxs-lookup"><span data-stu-id="b70aa-103">What's new in ASP.NET Core 1.1</span></span>

<span data-ttu-id="b70aa-104">ASP.NET Core 1.1 include le nuove funzionalità seguenti:</span><span class="sxs-lookup"><span data-stu-id="b70aa-104">ASP.NET Core 1.1 includes the following new features:</span></span>

- [<span data-ttu-id="b70aa-105">Middleware di riscrittura URL</span><span class="sxs-lookup"><span data-stu-id="b70aa-105">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
- [<span data-ttu-id="b70aa-106">Middleware di memorizzazione nella cache delle risposte</span><span class="sxs-lookup"><span data-stu-id="b70aa-106">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
- <span data-ttu-id="b70aa-107">[View Components as Tag Helpers](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper) (Visualizzare i componenti come helper per i tag)</span><span class="sxs-lookup"><span data-stu-id="b70aa-107">[View Components as Tag Helpers](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper)</span></span>
- <span data-ttu-id="b70aa-108">[Middleware as MVC filters](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline) (Middleware come filtri MVC)</span><span class="sxs-lookup"><span data-stu-id="b70aa-108">[Middleware as MVC filters](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline)</span></span>
- [<span data-ttu-id="b70aa-109">Cookieprovider TempData basato su</span><span class="sxs-lookup"><span data-stu-id="b70aa-109">Cookie-based TempData provider</span></span>](xref:fundamentals/app-state#tempdata)
- <span data-ttu-id="b70aa-110">[Azure App Service logging provider](xref:fundamentals/logging/index#azure-app-service-provider) (Provider di registrazione del Servizio app di Azure)</span><span class="sxs-lookup"><span data-stu-id="b70aa-110">[Azure App Service logging provider](xref:fundamentals/logging/index#azure-app-service-provider)</span></span>
- [<span data-ttu-id="b70aa-111">Provider di configurazione Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b70aa-111">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
- [<span data-ttu-id="b70aa-112">Repository della chiave di protezione dei dati di archiviazione di Azure e Redis</span><span class="sxs-lookup"><span data-stu-id="b70aa-112">Azure and Redis Storage Data Protection Key Repositories</span></span>](xref:security/data-protection/implementation/key-storage-providers)
- <span data-ttu-id="b70aa-113">Server WebListener per Windows</span><span class="sxs-lookup"><span data-stu-id="b70aa-113">WebListener Server for Windows</span></span>
- [<span data-ttu-id="b70aa-114">Supporto di WebSocket</span><span class="sxs-lookup"><span data-stu-id="b70aa-114">WebSockets support</span></span>](xref:fundamentals/websockets)

## <a name="choosing-between-versions-10-and-11-of-aspnet-core"></a><span data-ttu-id="b70aa-115">Scelta tra le versioni 1.0 e 1.1 di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b70aa-115">Choosing between versions 1.0 and 1.1 of ASP.NET Core</span></span>

<span data-ttu-id="b70aa-116">ASP.NET Core 1,1 dispone di più funzionalità rispetto a ASP.NET Core 1,0.</span><span class="sxs-lookup"><span data-stu-id="b70aa-116">ASP.NET Core 1.1 has more features than ASP.NET Core 1.0.</span></span> <span data-ttu-id="b70aa-117">In generale si consiglia di usare la versione più recente.</span><span class="sxs-lookup"><span data-stu-id="b70aa-117">In general, we recommend you use the latest version.</span></span>

## <a name="additional-information"></a><span data-ttu-id="b70aa-118">Informazioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b70aa-118">Additional Information</span></span>

- [<span data-ttu-id="b70aa-119">Note sulla versione di ASP.NET Core 1.1.0</span><span class="sxs-lookup"><span data-stu-id="b70aa-119">ASP.NET Core 1.1.0 Release Notes</span></span>](https://github.com/dotnet/aspnetcore/releases/tag/1.1.0)
- <span data-ttu-id="b70aa-120">Per essere aggiornati sull'avanzamento del lavoro e sui piani dei team di sviluppo di ASP.NET Core, partecipare alle riunioni in [ASP.NET Community Standup](https://live.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="b70aa-120">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
