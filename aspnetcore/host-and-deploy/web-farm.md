---
title: Ospitare ASP.NET Core in una Web farm
author: rick-anderson
description: Informazioni su come ospitare più istanze di un'app ASP.NET Core con risorse condivise in un ambiente Web farm.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/web-farm
ms.openlocfilehash: ee78e80a4eda3089943765700aa6bb62c6c1e07d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057518"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>Ospitare ASP.NET Core in una Web farm

Di [Chris Ross](https://github.com/Tratcher)

Una *Web farm* è un gruppo di due o più server Web (o *nodi*) che ospita più istanze di un'app. Quando arrivano richieste dagli utenti per una Web farm, un servizio di *bilanciamento del carico* distribuisce le richieste ai nodi della Web farm. Le Web farm consentono di migliorare:

* **Affidabilità/disponibilità**: quando uno o più nodi hanno esito negativo, il servizio di bilanciamento del carico può instradare le richieste ad altri nodi funzionanti per continuare a elaborare le richieste.
* **Capacità/prestazioni**: più nodi possono elaborare più richieste rispetto a un singolo server. Il servizio di bilanciamento del carico consente di bilanciare il carico di lavoro distribuendo le richieste ai nodi.
* **Scalabilità**: quando è necessaria una capacità maggiore o minore, il numero di nodi attivi può essere aumentato o ridotto in base al carico di lavoro. Le tecnologie della piattaforma per le Web farm, ad esempio [Servizio app di Azure](https://azure.microsoft.com/services/app-service/), possono aggiungere o rimuovere automaticamente nodi su richiesta dell'amministratore di sistema o automaticamente senza intervento umano.
* **Gestibilità**: i nodi di un Web farm possono basarsi su un set di servizi condivisi, semplificando la gestione del sistema. Ad esempio, i nodi di una Web farm possono basarsi su un singolo server di database e un percorso di rete comune per le risorse statiche, ad esempio immagini e file scaricabili.

Questo argomento descrive la configurazione e le dipendenze per le app ASP.NET Core ospitate in una Web farm che si basano su risorse condivise.

## <a name="general-configuration"></a>Configurazione generale

<xref:host-and-deploy/index>  
Informazioni sulla configurazione degli ambienti host e sulla distribuzione delle app ASP.NET Core. Configurare un gestore di processi in ogni nodo della Web farm per automatizzare avvii e riavvii dell'app. Ogni nodo richiede il runtime di ASP.NET Core. Per altre informazioni, vedere gli argomenti nell'area [Ospitare e distribuire](xref:host-and-deploy/index) della documentazione.

<xref:host-and-deploy/proxy-load-balancer>  
Informazioni sulla configurazione per le app ospitate dietro server proxy e servizi di bilanciamento del carico, che spesso nascondono informazioni importanti sulle richieste.

<xref:host-and-deploy/azure-apps/index>  
Il [servizio app di Azure](https://azure.microsoft.com/services/app-service/) è un [servizio di piattaforma di cloud computing Microsoft](https://azure.microsoft.com/) per l'hosting di app Web, inclusa ASP.NET Core. Servizio app è una piattaforma completamente gestita che offre scalabilità automatica, bilanciamento del carico, applicazione di patch e distribuzione continua.

## <a name="app-data"></a>Dati dell'app

Quando un'app viene distribuita su più istanze, potrebbe essere necessario condividere informazioni sullo stato dell'app tra i nodi. Se lo stato è temporaneo, valutare la condivisione di una [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). Se lo stato condiviso deve essere persistente, valutare la possibilità di archiviare lo stato condiviso in un database.

## <a name="required-configuration"></a>Configurazione necessaria

Occorre configurare la protezione dei dati e la memorizzazione nella cache per le app distribuite in una Web farm.

### <a name="data-protection"></a>Protezione dati

Il [sistema di protezione dei dati di ASP.NET Core](xref:security/data-protection/introduction) viene usato dalle app per proteggere i dati. La protezione dei dati si basa su un set di chiavi di crittografia archiviate in un *KeyRing*. Quando il sistema di protezione dei dati viene inizializzato, vengono applicate le [impostazioni predefinite](xref:security/data-protection/configuration/default-settings) che archiviano il KeyRing in locale. In base alla configurazione predefinita, viene archiviato un unico KeyRing in ogni nodo della Web farm. Di conseguenza, ogni nodo della Web farm non può decrittografare i dati crittografati da un'app su qualsiasi altro nodo. La configurazione predefinita non è in genere adatta per l'hosting di app in una Web farm. In alternativa all'implementazione di un KeyRing condiviso, è sempre possibile indirizzare le richieste utente allo stesso nodo. Per altre informazioni sulla configurazione del sistema di protezione dei dati per le distribuzioni di Web farm, vedere <xref:security/data-protection/configuration/overview>.

### <a name="caching"></a>Memorizzazione nella cache

In un ambiente Web farm, il meccanismo di memorizzazione nella cache deve condividere gli elementi memorizzati nella cache tra i nodi della Web farm. La memorizzazione nella cache deve basarsi su una cache Redis comune, un database di SQL Server condiviso o un'implementazione personalizzata di memorizzazione nella cache che condivide gli elementi memorizzati nella cache nella Web farm. Per altre informazioni, vedere <xref:performance/caching/distributed>.

## <a name="dependent-components"></a>Componenti dipendenti

Gli scenari seguenti non richiedono configurazioni aggiuntive, ma dipendono da tecnologie che devono essere configurate per le Web farm.

| Scenario | Dipende da &hellip; |
| -------- | ------------------- |
| Authentication | Protezione dei dati (vedere <xref:security/data-protection/configuration/overview>).<br><br>Per altre informazioni, vedere <xref:security/authentication/cookie> e <xref:security/cookie-sharing>. |
| Identity | Configurazione di autenticazione e database.<br><br>Per altre informazioni, vedere <xref:security/authentication/identity>. |
| sessione | Protezione dei dati (informazioni crittografate) cookie (vedere <xref:security/data-protection/configuration/overview> ) e memorizzazione nella cache (vedere <xref:performance/caching/distributed> ).<br><br>Per ulteriori informazioni, vedere [sessione e gestione dello stato: stato della sessione](xref:fundamentals/app-state#session-state). |
| TempData | Protezione dei dati (informazioni crittografate cookie ) (vedere <xref:security/data-protection/configuration/overview> ) o sessione (vedere [gestione di sessioni e Stati: stato sessione](xref:fundamentals/app-state#session-state)).<br><br>Per ulteriori informazioni, vedere [gestione delle sessioni e dello stato: TempData](xref:fundamentals/app-state#tempdata). |
| Antifalsificazione | Protezione dei dati (vedere <xref:security/data-protection/configuration/overview>).<br><br>Per altre informazioni, vedere <xref:security/anti-request-forgery>. |

## <a name="troubleshoot"></a>Risolvere problemi

### <a name="data-protection-and-caching"></a>Protezione dei dati e memorizzazione nella cache

Quando la protezione dei dati o la memorizzazione nella cache non è configurata per un ambiente Web farm, si verificano errori intermittenti durante l'elaborazione delle richieste. Ciò si verifica perché i nodi non condividono le stesse risorse e le richieste degli utenti non vengono sempre indirizzate allo stesso nodo.

Si consideri un utente che accede all'app usando cookie l'autenticazione di. L'utente acceda all'app in un nodo della Web farm. Se la richiesta successiva arriva nello stesso nodo in cui è stato eseguito l'accesso, l'app è in grado di decrittografare l'autenticazione cookie e consente l'accesso alla risorsa dell'app. Se la richiesta successiva arriva a un nodo diverso, l'app non può decrittografare l'autenticazione cookie dal nodo in cui l'utente ha effettuato l'accesso e l'autorizzazione per la risorsa richiesta ha esito negativo.

Quando si verificano **intermittenti** di uno dei seguenti sintomi, il problema viene in genere tracciato a una configurazione di protezione dei dati o di memorizzazione nella cache non corretta per un ambiente Web farm:

* Interruzioni di autenticazione: l'autenticazione cookie non è configurata correttamente o non può essere decrittografata. Gli accessi OAuth (Facebook, Microsoft, Twitter) o OpenIdConnect non riescono con l'errore "Correlazione non riuscita."
* Interruzioni di autorizzazione: Identity viene persa.
* Perdita dei dati per lo stato della sessione.
* Gli elementi memorizzati nella cache scompaiono.
* Errore di TempData.
* I post hanno esito negativo: il controllo anti-falsificazione ha esito negativo.

Per altre informazioni sulla configurazione della protezione dei dati per le distribuzioni di Web farm, vedere <xref:security/data-protection/configuration/overview>. Per altre informazioni sulla configurazione della memorizzazione nella cache per le distribuzioni di Web farm, vedere <xref:performance/caching/distributed>.

## <a name="obtain-data-from-apps"></a>Ottenere dati dalle app

Se le app della Web farm sono in grado di rispondere alle richieste, è possibile ottenere dati sulle richieste, le connessioni e altri dati dalle app tramite middleware inline di terminale. Per altre informazioni e codice di esempio, vedere <xref:test/troubleshoot#obtain-data-from-an-app>.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Estensione script personalizzata per Windows](/azure/virtual-machines/extensions/custom-script-windows): Scarica ed esegue script in macchine virtuali di Azure, utile per la configurazione post-distribuzione e l'installazione del software.
* <xref:host-and-deploy/proxy-load-balancer>
 