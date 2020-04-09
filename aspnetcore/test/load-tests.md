---
title: test di carico/stress di ASP.NET Core
author: Jeremy-Meng
description: Scopri diversi strumenti e approcci importanti per i test di carico e i test di stress ASP.NET le app Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 1fd77a767fb53b9276081dd712e13108094a0382
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664689"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="f0ee7-103">test di carico/stress di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f0ee7-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="f0ee7-104">I test di carico e i test di stress sono importanti per garantire che un'app Web sia performante e scalabile.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="f0ee7-105">I loro obiettivi sono diversi anche se spesso condividono test simili.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="f0ee7-106">**Test di** &ndash; carico Verifica se l'app è in grado di gestire un carico specificato di utenti per un determinato scenario pur soddisfacendo l'obiettivo di risposta.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="f0ee7-107">L'app viene eseguita in condizioni normali.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="f0ee7-108">**Test di stress** &ndash; Testare la stabilità dell'app durante l'esecuzione in condizioni estreme, spesso per un lungo periodo di tempo.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="f0ee7-109">I test collocano un carico utente elevato, un picco o un carico in graduale, nell'app oppure limitano le risorse di elaborazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="f0ee7-110">I test di stress determinano se un'app sotto stress può essere ripristinata da un errore e tornare normalmente al comportamento previsto.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="f0ee7-111">In condizioni di stress, l'app non viene eseguita in condizioni normali.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="f0ee7-112">Visual Studio 2019 è l'ultima versione di Visual Studio con funzionalità del test di carico.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="f0ee7-113">Per i clienti che richiedono strumenti di test di carico in futuro, sono consigliati strumenti alternativi, ad esempio Apache JMeter, Akamai CloudTest e BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="f0ee7-114">Per ulteriori informazioni, vedere le note sulla versione di [Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="f0ee7-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="f0ee7-115">Strumenti di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0ee7-115">Visual Studio tools</span></span>

<span data-ttu-id="f0ee7-116">Visual Studio consente agli utenti di creare, sviluppare ed eseguire il debug di test di carico e prestazioni Web.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="f0ee7-117">È disponibile un'opzione per creare test registrando le azioni in un browser Web.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="f0ee7-118">Per informazioni su come creare, configurare ed eseguire progetti di test di carico con Visual Studio 2017, vedere [Guida introduttiva: Creare un progetto di test](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)di carico.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="f0ee7-119">I test di carico possono essere configurati per l'esecuzione locale o nel cloud usando DevOps di Azure.Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="f0ee7-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="f0ee7-120">Strumenti di terzi</span><span class="sxs-lookup"><span data-stu-id="f0ee7-120">Third-party tools</span></span>

<span data-ttu-id="f0ee7-121">L'elenco seguente contiene strumenti per le prestazioni Web di terze parti con vari set di funzionalità:</span><span class="sxs-lookup"><span data-stu-id="f0ee7-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="f0ee7-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="f0ee7-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="f0ee7-123">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="f0ee7-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="f0ee7-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="f0ee7-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="f0ee7-125">k6</span><span class="sxs-lookup"><span data-stu-id="f0ee7-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="f0ee7-126">Locust</span><span class="sxs-lookup"><span data-stu-id="f0ee7-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="f0ee7-127">WebSurge del Vento Occidentale</span><span class="sxs-lookup"><span data-stu-id="f0ee7-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="f0ee7-128">Netling</span><span class="sxs-lookup"><span data-stu-id="f0ee7-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="f0ee7-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="f0ee7-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

