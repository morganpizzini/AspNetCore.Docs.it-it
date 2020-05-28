---
title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core test di carico/stress

Test di carico e test di stress sono importanti per garantire che un'app Web sia efficiente e scalabile. I rispettivi obiettivi sono diversi anche se condividono spesso test simili.

**Test di carico**: verificare se l'app è in grado di gestire un carico specificato di utenti per un determinato scenario soddisfacendo comunque l'obiettivo della risposta. L'app viene eseguita in condizioni normali.

**Test di stress**: verifica la stabilità dell'app quando viene eseguita in condizioni estreme, spesso per un lungo periodo di tempo. I test inseriscono un carico utente elevato, picchi o aumento graduale del carico, sull'app o limitano le risorse di elaborazione dell'app.

I test di stress determinano se un'app in stress può recuperare da un errore e restituire normalmente il comportamento previsto. In condizioni di stress, l'app non viene eseguita in condizioni normali.

Visual Studio 2019 è l'ultima versione di Visual Studio con le funzionalità del test di carico. Per i clienti che necessitano di strumenti di test di carico in futuro, è consigliabile usare strumenti alternativi, ad esempio Apache JMeter, Akamai CloudTest e BlazeMeter. Per ulteriori informazioni, vedere le [Note sulla versione di Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

## <a name="visual-studio-tools"></a>Strumenti di Visual Studio

Visual Studio consente agli utenti di creare, sviluppare ed eseguire il debug di test di carico e prestazioni Web. È disponibile un'opzione per la creazione di test mediante la registrazione di azioni in un Web browser.

Per informazioni su come creare, configurare ed eseguire i progetti di test di carico usando Visual Studio 2017, vedere [Guida introduttiva: creare un progetto di test di carico](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

I test di carico possono essere configurati per l'esecuzione in locale o in esecuzione nel cloud usando Azure DevOps.

## <a name="third-party-tools"></a>Strumenti di terzi

L'elenco seguente contiene gli strumenti per le prestazioni Web di terze parti con diversi set di funzionalità:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (AB)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [K6](https://k6.io)
* [Locusta](https://locust.io/)
* [Websurge di Wind West](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

