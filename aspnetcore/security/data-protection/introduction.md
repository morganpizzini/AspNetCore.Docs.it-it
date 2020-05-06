---
title: Protezione dei dati ASP.NET Core
author: rick-anderson
description: Informazioni sul concetto di protezione dei dati e sui principi di progettazione delle API di protezione dei dati ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/introduction
ms.openlocfilehash: db2c22454fc6c7e663ca603e9d70b6c12ce31af4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775804"
---
# <a name="aspnet-core-data-protection"></a>Protezione dei dati ASP.NET Core

Le applicazioni Web spesso devono archiviare dati sensibili alla sicurezza. Windows fornisce DPAPI per le applicazioni desktop, ma questo non è adatto per le applicazioni Web. Lo stack di protezione dei dati ASP.NET Core fornisce un'API di crittografia semplice e facile da usare che uno sviluppatore può usare per proteggere i dati, tra cui la gestione e la rotazione delle chiavi.

Lo stack di protezione dei dati ASP.NET Core è progettato per fungere da sostituzione a lungo termine &lt;per&gt; l'elemento machineKey in ASP.NET 1. x-4. x. È stato progettato per risolvere molti dei difetti del vecchio stack di crittografia, offrendo al tempo stesso una soluzione predefinita per la maggior parte dei casi d'uso che è probabile che si verifichino le applicazioni moderne.

## <a name="problem-statement"></a>Presentazione del problema

L'informativa sul problema generale può essere concisa in una singola frase: è necessario rendere persistenti le informazioni attendibili per il recupero successivo, ma non si considera attendibile il meccanismo di persistenza. In termini Web, questo potrebbe essere scritto come "è necessario eseguire il round trip di stato attendibile tramite un client non attendibile".

L'esempio canonico è un cookie di autenticazione o bearer token. Il server genera un token "I am Groot and have XYZ Permissions" e lo passa al client. In una data futura il client presenta il token al server, ma il server richiede un certo tipo di garanzia che il client non abbia falsificato il token. Quindi, il primo requisito: autenticità (noto anche come integrità, correzione delle manomissioni.

Poiché lo stato permanente è considerato attendibile dal server, si prevede che questo stato possa contenere informazioni specifiche per l'ambiente operativo. Il formato potrebbe essere un percorso di file, un'autorizzazione, un handle o un altro riferimento indiretto o altri dati specifici del server. Tali informazioni in genere non devono essere divulgate a un client non attendibile. Quindi, il secondo requisito: riservatezza.

Infine, poiché le applicazioni moderne sono componenti, ciò che abbiamo visto è che i singoli componenti vorranno sfruttare questo sistema senza considerare gli altri componenti del sistema. Ad esempio, se un componente bearer token usa questo stack, dovrebbe funzionare senza interferenze da un meccanismo anti-CSRF che potrebbe anche usare lo stesso stack. Quindi, il requisito finale: Isolation.

Microsoft può fornire ulteriori vincoli per limitare l'ambito dei requisiti. Si presuppone che tutti i servizi che operano all'interno di cryptosystem siano ugualmente attendibili e che i dati non debbano essere generati o utilizzati al di fuori dei servizi sotto il controllo diretto. Inoltre, è necessario che le operazioni siano il più veloci possibile poiché ogni richiesta al servizio Web potrebbe passare attraverso il cryptosystem una o più volte. Questo rende la crittografia simmetrica ideale per questo scenario ed è possibile scontare la crittografia asimmetrica fino a un momento specifico.

## <a name="design-philosophy"></a>Filosofia di progettazione

È stata avviata l'identificazione dei problemi con lo stack esistente. Una volta completata questa situazione, abbiamo esaminato il panorama delle soluzioni esistenti ed è stato concluso che nessuna soluzione esistente aveva le funzionalità richieste. È stata quindi progettata una soluzione basata su diversi principi di guida.

* Il sistema deve offrire semplicità di configurazione. Idealmente, la configurazione del sistema è zero e gli sviluppatori potrebbero raggiungere il terreno. Nelle situazioni in cui gli sviluppatori devono configurare un aspetto specifico, ad esempio il repository delle chiavi, è necessario considerare la necessità di rendere semplici le configurazioni specifiche.

* Offrire un'API semplice per gli utenti. Le API dovrebbero essere facili da usare correttamente e difficili da usare in modo non corretto.

* Gli sviluppatori non devono apprendere principi di gestione delle chiavi. Il sistema deve gestire la selezione dell'algoritmo e la durata della chiave per conto dello sviluppatore. Idealmente, lo sviluppatore non deve mai neanche avere accesso al materiale della chiave non elaborata.

* Le chiavi devono essere protette quando possibile. Il sistema deve individuare un meccanismo di protezione predefinito appropriato e applicarlo automaticamente.

Con questi principi, abbiamo sviluppato uno stack di protezione dei dati semplice e semplice [da usare](xref:security/data-protection/using-data-protection) .

Le API di protezione dei dati ASP.NET Core non sono destinate principalmente alla persistenza illimitata dei payload riservati. Altre tecnologie come [DPAPI di Windows](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) e [Rights Management di Azure](/rights-management/) sono più adatte allo scenario di archiviazione indefinita e hanno funzionalità di gestione delle chiavi corrispondenti. Detto questo, non c'è nulla che impedisce agli sviluppatori di usare le API di protezione dei dati ASP.NET Core per la protezione a lungo termine dei dati riservati.

## <a name="audience"></a>Destinatari

Il sistema di protezione dei dati è suddiviso in cinque pacchetti principali. I vari aspetti di queste API sono destinati a tre destinatari principali;

1. La [Panoramica delle API consumer](xref:security/data-protection/consumer-apis/overview) è destinata agli sviluppatori di applicazioni e Framework.

   "Non voglio conoscere il funzionamento dello stack o la relativa configurazione. Desidero semplicemente eseguire alcune operazioni nel modo più semplice possibile con la probabilità elevata di usare le API con successo. "

2. Le [API di configurazione](xref:security/data-protection/configuration/overview) sono destinate agli sviluppatori di applicazioni e agli amministratori di sistema.

   "Devo indicare al sistema di protezione dei dati che l'ambiente richiede percorsi o impostazioni non predefinite".

3. Le API di estendibilità sono destinate agli sviluppatori responsabili dell'implementazione dei criteri personalizzati. L'utilizzo di queste API potrebbe essere limitato a situazioni rare e a sviluppatori esperti di sicurezza.

   "È necessario sostituire un intero componente all'interno del sistema perché sono presenti requisiti di comportamento realmente univoci. Sono intenzionato a imparare le parti non comuni della superficie dell'API per creare un plug-in che soddisfi i requisiti ".

## <a name="package-layout"></a>Layout del pacchetto

Lo stack di protezione dei dati è costituito da cinque pacchetti.

* [Microsoft. AspNetCore. dataprotection. abstracts](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) contiene le <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> interfacce <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> e per la creazione di servizi di protezione dei dati. Contiene inoltre metodi di estensione utili per l'utilizzo di questi tipi (ad esempio, [IDataProtector. Protect](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*)). Se viene creata un'istanza del sistema di protezione dei dati in un'altra posizione e si utilizza `Microsoft.AspNetCore.DataProtection.Abstractions`l'API, fare riferimento a.

* [Microsoft. AspNetCore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) contiene l'implementazione principale del sistema di protezione dei dati, tra cui operazioni di crittografia di base, gestione delle chiavi, configurazione ed estensibilità. Per creare un'istanza del sistema di protezione dei dati (ad esempio, aggiungendolo a un <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>) o modificando o estendendo il comportamento, fare riferimento `Microsoft.AspNetCore.DataProtection`a.

* [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contiene altre API che gli sviluppatori possono trovare utili ma che non appartengono al pacchetto principale. Ad esempio, questo pacchetto contiene i metodi factory per creare un'istanza del sistema di protezione dati per archiviare le chiavi in una posizione nella file system senza inserimento <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>delle dipendenze (vedere). Contiene inoltre i metodi di estensione per limitare la durata dei payload protetti (vedere <xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector>).

* [Microsoft. AspNetCore. dataprotection. systemWeb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) può essere installato in un'app ASP.NET 4. x esistente per reindirizzare le `<machineKey>` operazioni per l'uso del nuovo ASP.NET Core stack di protezione dei dati. Per altre informazioni, vedere <xref:security/data-protection/compatibility/replacing-machinekey>.

* [Microsoft. AspNetCore. Cryptography. Derivation](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) fornisce un'implementazione della routine di hashing delle password PBKDF2 e può essere utilizzata dai sistemi che devono gestire le password utente in modo sicuro. Per altre informazioni, vedere <xref:security/data-protection/consumer-apis/password-hashing>.

## <a name="additional-resources"></a>Risorse aggiuntive

<xref:host-and-deploy/web-farm>
