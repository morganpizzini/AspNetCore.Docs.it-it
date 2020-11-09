---
title: ASP.NET Core test di carico/stress
author: Jeremy-Meng
description: Informazioni sui diversi strumenti e approcci rilevanti per test di carico e test di stress ASP.NET Core app.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: test/loadtests
ms.openlocfilehash: 56f5a5caeea7581e26f8d8cec9662f439cd24b9e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060716"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="bfca2-103">ASP.NET Core test di carico/stress</span><span class="sxs-lookup"><span data-stu-id="bfca2-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="bfca2-104">Test di carico e test di stress sono importanti per garantire che un'app Web sia efficiente e scalabile.</span><span class="sxs-lookup"><span data-stu-id="bfca2-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="bfca2-105">I rispettivi obiettivi sono diversi anche se condividono spesso test simili.</span><span class="sxs-lookup"><span data-stu-id="bfca2-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="bfca2-106">**Test di carico** : verificare se l'app è in grado di gestire un carico specificato di utenti per un determinato scenario soddisfacendo comunque l'obiettivo della risposta.</span><span class="sxs-lookup"><span data-stu-id="bfca2-106">**Load tests** : Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="bfca2-107">L'app viene eseguita in condizioni normali.</span><span class="sxs-lookup"><span data-stu-id="bfca2-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="bfca2-108">**Test di stress** : verifica la stabilità dell'app quando viene eseguita in condizioni estreme, spesso per un lungo periodo di tempo.</span><span class="sxs-lookup"><span data-stu-id="bfca2-108">**Stress tests** : Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="bfca2-109">I test inseriscono un carico utente elevato, picchi o aumento graduale del carico, sull'app o limitano le risorse di elaborazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="bfca2-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="bfca2-110">I test di stress determinano se un'app in stress può recuperare da un errore e restituire normalmente il comportamento previsto.</span><span class="sxs-lookup"><span data-stu-id="bfca2-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="bfca2-111">In condizioni di stress, l'app non viene eseguita in condizioni normali.</span><span class="sxs-lookup"><span data-stu-id="bfca2-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="bfca2-112">Visual Studio 2019 ha annunciato piani per [deprecare il test di carico](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="bfca2-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="bfca2-113">Il servizio di test di carico basato sul cloud di Azure DevOps corrispondente è stato chiuso.</span><span class="sxs-lookup"><span data-stu-id="bfca2-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="bfca2-114">Strumenti di terzi</span><span class="sxs-lookup"><span data-stu-id="bfca2-114">Third-party tools</span></span>

<span data-ttu-id="bfca2-115">L'elenco seguente contiene gli strumenti per le prestazioni Web di terze parti con diversi set di funzionalità:</span><span class="sxs-lookup"><span data-stu-id="bfca2-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="bfca2-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="bfca2-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="bfca2-117">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="bfca2-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="bfca2-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="bfca2-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="bfca2-119">K6</span><span class="sxs-lookup"><span data-stu-id="bfca2-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="bfca2-120">Locusta</span><span class="sxs-lookup"><span data-stu-id="bfca2-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="bfca2-121">Websurge di Wind West</span><span class="sxs-lookup"><span data-stu-id="bfca2-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="bfca2-122">Netling</span><span class="sxs-lookup"><span data-stu-id="bfca2-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="bfca2-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="bfca2-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)