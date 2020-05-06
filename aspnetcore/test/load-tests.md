---
title: ASP.NET Core test di carico/stress
author: Jeremy-Meng
description: Informazioni sui diversi strumenti e approcci rilevanti per test di carico e test di stress ASP.NET Core app.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: cf99eaa71846ea705a312b0fb773605fc77b0d97
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775258"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="ab6e7-103">ASP.NET Core test di carico/stress</span><span class="sxs-lookup"><span data-stu-id="ab6e7-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="ab6e7-104">Test di carico e test di stress sono importanti per garantire che un'app Web sia efficiente e scalabile.</span><span class="sxs-lookup"><span data-stu-id="ab6e7-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="ab6e7-105">I rispettivi obiettivi sono diversi anche se condividono spesso test simili.</span><span class="sxs-lookup"><span data-stu-id="ab6e7-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="ab6e7-106">I **test** &ndash; di carico testano se l'app è in grado di gestire un carico specificato di utenti per un determinato scenario soddisfacendo comunque l'obiettivo della risposta.</span><span class="sxs-lookup"><span data-stu-id="ab6e7-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="ab6e7-107">L'app viene eseguita in condizioni normali.</span><span class="sxs-lookup"><span data-stu-id="ab6e7-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="ab6e7-108">I **test** &ndash; di stress testano la stabilità dell'app durante l'esecuzione in condizioni estreme, spesso per un lungo periodo di tempo.</span><span class="sxs-lookup"><span data-stu-id="ab6e7-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="ab6e7-109">I test inseriscono un carico utente elevato, picchi o aumento graduale del carico, sull'app o limitano le risorse di elaborazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="ab6e7-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="ab6e7-110">I test di stress determinano se un'app in stress può recuperare da un errore e restituire normalmente il comportamento previsto.</span><span class="sxs-lookup"><span data-stu-id="ab6e7-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="ab6e7-111">In condizioni di stress, l'app non viene eseguita in condizioni normali.</span><span class="sxs-lookup"><span data-stu-id="ab6e7-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="ab6e7-112">Visual Studio 2019 è l'ultima versione di Visual Studio con le funzionalità del test di carico.</span><span class="sxs-lookup"><span data-stu-id="ab6e7-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="ab6e7-113">Per i clienti che necessitano di strumenti di test di carico in futuro, è consigliabile usare strumenti alternativi, ad esempio Apache JMeter, Akamai CloudTest e BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="ab6e7-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="ab6e7-114">Per ulteriori informazioni, vedere le [Note sulla versione di Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="ab6e7-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="ab6e7-115">Strumenti di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab6e7-115">Visual Studio tools</span></span>

<span data-ttu-id="ab6e7-116">Visual Studio consente agli utenti di creare, sviluppare ed eseguire il debug di test di carico e prestazioni Web.</span><span class="sxs-lookup"><span data-stu-id="ab6e7-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="ab6e7-117">È disponibile un'opzione per la creazione di test mediante la registrazione di azioni in un Web browser.</span><span class="sxs-lookup"><span data-stu-id="ab6e7-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="ab6e7-118">Per informazioni su come creare, configurare ed eseguire i progetti di test di carico usando Visual Studio 2017, vedere [Guida introduttiva: creare un progetto di test di carico](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="ab6e7-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="ab6e7-119">I test di carico possono essere configurati per l'esecuzione in locale o in esecuzione nel cloud usando Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="ab6e7-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="ab6e7-120">Strumenti di terzi</span><span class="sxs-lookup"><span data-stu-id="ab6e7-120">Third-party tools</span></span>

<span data-ttu-id="ab6e7-121">L'elenco seguente contiene gli strumenti per le prestazioni Web di terze parti con diversi set di funzionalità:</span><span class="sxs-lookup"><span data-stu-id="ab6e7-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="ab6e7-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="ab6e7-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="ab6e7-123">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="ab6e7-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="ab6e7-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="ab6e7-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="ab6e7-125">K6</span><span class="sxs-lookup"><span data-stu-id="ab6e7-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="ab6e7-126">Locusta</span><span class="sxs-lookup"><span data-stu-id="ab6e7-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="ab6e7-127">Websurge di Wind West</span><span class="sxs-lookup"><span data-stu-id="ab6e7-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="ab6e7-128">Netling</span><span class="sxs-lookup"><span data-stu-id="ab6e7-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="ab6e7-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="ab6e7-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

