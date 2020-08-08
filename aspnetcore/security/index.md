---
title: Panoramica sulla sicurezza di ASP.NET Core
author: rick-anderson
description: Informazioni sui concetti di base di autenticazione, autorizzazione e sicurezza in ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/index
ms.openlocfilehash: ead5863f0656e182d395fc1e0deaf63ff66034cb
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021367"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="3dbcb-103">Panoramica sulla sicurezza di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3dbcb-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="3dbcb-104">ASP.NET Core consente agli sviluppatori di configurare e gestire facilmente la sicurezza per le proprie app.</span><span class="sxs-lookup"><span data-stu-id="3dbcb-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="3dbcb-105">ASP.NET Core contiene funzionalità per gestire l'autenticazione, l'autorizzazione, la protezione dei dati, l'applicazione di HTTPS, i segreti dell'app, la protezione dalla falsificazione delle richieste e la gestione di CORS.</span><span class="sxs-lookup"><span data-stu-id="3dbcb-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="3dbcb-106">Queste funzionalità di sicurezza consentono di compilare app ASP.NET Core solide e sicure.</span><span class="sxs-lookup"><span data-stu-id="3dbcb-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="3dbcb-107">Funzionalità di sicurezza di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3dbcb-107">ASP.NET Core security features</span></span>

<span data-ttu-id="3dbcb-108">ASP.NET Core offre molti strumenti e librerie per proteggere le app, inclusi i provider di identità incorporati, ma è possibile usare servizi di identità di terze parti, ad esempio Facebook, Twitter e LinkedIn.</span><span class="sxs-lookup"><span data-stu-id="3dbcb-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in identity providers, but you can use third-party identity services such as Facebook, Twitter, and LinkedIn.</span></span> <span data-ttu-id="3dbcb-109">Con ASP.NET Core, è possibile gestire facilmente i segreti dell'app, che consentono di archiviare e usare informazioni riservate senza la necessità di esporle nel codice.</span><span class="sxs-lookup"><span data-stu-id="3dbcb-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="3dbcb-110">Autenticazione e autorizzazione</span><span class="sxs-lookup"><span data-stu-id="3dbcb-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="3dbcb-111">L'autenticazione è un processo in cui un utente fornisce le credenziali che vengono quindi confrontate con quelle archiviate in un sistema operativo, un database, un'applicazione o una risorsa.</span><span class="sxs-lookup"><span data-stu-id="3dbcb-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="3dbcb-112">Se corrispondono, gli utenti vengono autenticati correttamente e quindi possono eseguire azioni per cui sono autorizzati, durante un processo di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="3dbcb-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="3dbcb-113">L'autorizzazione è il processo che determina quali operazioni può eseguire un utente.</span><span class="sxs-lookup"><span data-stu-id="3dbcb-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="3dbcb-114">È possibile considerare l'autenticazione come un modo per entrare in uno spazio, ad esempio un server, un database, un'app o una risorsa, mentre l'autorizzazione definisce le azioni che l'utente può eseguire e su quali oggetti all'interno di tale spazio (server, database o app).</span><span class="sxs-lookup"><span data-stu-id="3dbcb-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="3dbcb-115">Vulnerabilità comuni nel software</span><span class="sxs-lookup"><span data-stu-id="3dbcb-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="3dbcb-116">ASP.NET Core ed Entity Framework contengono funzionalità che consentono di proteggere le app e impedire violazioni della sicurezza.</span><span class="sxs-lookup"><span data-stu-id="3dbcb-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="3dbcb-117">L'elenco di collegamenti seguente offre documentazione in cui sono descritte le tecniche per evitare le vulnerabilità della sicurezza più comuni nelle app Web:</span><span class="sxs-lookup"><span data-stu-id="3dbcb-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="3dbcb-118">Attacchi di tipo cross-site scripting (XSS)</span><span class="sxs-lookup"><span data-stu-id="3dbcb-118">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="3dbcb-119">Attacchi SQL injection</span><span class="sxs-lookup"><span data-stu-id="3dbcb-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="3dbcb-120">Richieste intersito false</span><span class="sxs-lookup"><span data-stu-id="3dbcb-120">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="3dbcb-121">Attacchi di reindirizzamento aperti</span><span class="sxs-lookup"><span data-stu-id="3dbcb-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="3dbcb-122">Esistono altre vulnerabilità di cui è necessario essere consapevoli.</span><span class="sxs-lookup"><span data-stu-id="3dbcb-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="3dbcb-123">Per ulteriori informazioni, vedere gli altri articoli nella sezione \*\*sicurezza e Identity \*\* contenuto del sommario.</span><span class="sxs-lookup"><span data-stu-id="3dbcb-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
