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
# <a name="aspnet-core-loadstress-testing"></a>test di carico/stress di ASP.NET Core

I test di carico e i test di stress sono importanti per garantire che un'app Web sia performante e scalabile. I loro obiettivi sono diversi anche se spesso condividono test simili.

**Test di** &ndash; carico Verifica se l'app è in grado di gestire un carico specificato di utenti per un determinato scenario pur soddisfacendo l'obiettivo di risposta. L'app viene eseguita in condizioni normali.

**Test di stress** &ndash; Testare la stabilità dell'app durante l'esecuzione in condizioni estreme, spesso per un lungo periodo di tempo. I test collocano un carico utente elevato, un picco o un carico in graduale, nell'app oppure limitano le risorse di elaborazione dell'app.

I test di stress determinano se un'app sotto stress può essere ripristinata da un errore e tornare normalmente al comportamento previsto. In condizioni di stress, l'app non viene eseguita in condizioni normali.

Visual Studio 2019 è l'ultima versione di Visual Studio con funzionalità del test di carico. Per i clienti che richiedono strumenti di test di carico in futuro, sono consigliati strumenti alternativi, ad esempio Apache JMeter, Akamai CloudTest e BlazeMeter. Per ulteriori informazioni, vedere le note sulla versione di [Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

## <a name="visual-studio-tools"></a>Strumenti di Visual Studio

Visual Studio consente agli utenti di creare, sviluppare ed eseguire il debug di test di carico e prestazioni Web. È disponibile un'opzione per creare test registrando le azioni in un browser Web.

Per informazioni su come creare, configurare ed eseguire progetti di test di carico con Visual Studio 2017, vedere [Guida introduttiva: Creare un progetto di test](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)di carico.

I test di carico possono essere configurati per l'esecuzione locale o nel cloud usando DevOps di Azure.Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.

## <a name="third-party-tools"></a>Strumenti di terzi

L'elenco seguente contiene strumenti per le prestazioni Web di terze parti con vari set di funzionalità:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [WebSurge del Vento Occidentale](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

