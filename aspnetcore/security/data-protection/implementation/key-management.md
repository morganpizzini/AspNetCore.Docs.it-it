---
title: Gestione delle chiavi in ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione delle API di gestione delle chiavi ASP.NET Core Data Protection.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: 68913d13c97ba6be73dabf79e03c146a37388ad3
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408942"
---
# <a name="key-management-in-aspnet-core"></a>Gestione delle chiavi in ASP.NET Core

<a name="data-protection-implementation-key-management"></a>

Il sistema di protezione dei dati gestisce automaticamente la durata delle chiavi master utilizzate per proteggere e rimuovere la protezione dei payload. Ogni chiave può esistere in una delle quattro fasi seguenti:

* Creato: la chiave esiste nell'anello chiave, ma non è ancora stata attivata. La chiave non deve essere usata per le nuove operazioni di protezione fino a quando non è trascorso tempo sufficiente che la chiave abbia avuto la possibilità di propagarsi a tutti i computer che utilizzano questo anello di chiave.

* Attivo: la chiave esiste nell'anello chiave e deve essere usata per tutte le nuove operazioni di protezione.

* Scaduto: la chiave ha eseguito la durata naturale e non deve più essere usata per le nuove operazioni di protezione.

* Revocata: la chiave viene compromessa e non deve essere usata per le nuove operazioni di protezione.

È possibile utilizzare tutte le chiavi create, Active e expire per rimuovere la protezione dai payload in ingresso. Per impostazione predefinita, le chiavi revocate non possono essere usate per rimuovere la protezione dei payload, ma lo sviluppatore dell'applicazione può [eseguire l'override di questo comportamento](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) , se necessario.

>[!WARNING]
> Lo sviluppatore potrebbe essere tentato di eliminare una chiave dall'anello chiave, ad esempio eliminando il file corrispondente dal file system. A questo punto, tutti i dati protetti dalla chiave sono permanentemente decifrabili e non esiste alcun override di emergenza, ad esempio con chiavi revocate. L'eliminazione di una chiave è effettivamente un comportamento distruttivo e, di conseguenza, il sistema di protezione dei dati non espone alcuna API di prima classe per l'esecuzione di questa operazione.

## <a name="default-key-selection"></a>Selezione chiave predefinita

Quando il sistema di protezione dei dati legge l'anello di chiave dal repository sottostante, tenterà di individuare una chiave "predefinita" dall'anello di chiave. La chiave predefinita viene usata per le nuove operazioni di protezione.

L'euristica generale è che il sistema di protezione dei dati sceglie la chiave con la data di attivazione più recente come chiave predefinita. (È presente un piccolo fattore di fudge per consentire lo sfasamento di clock da server a server). Se la chiave è scaduta o revocata e se l'applicazione non ha disabilitato la generazione automatica delle chiavi, verrà generata una nuova chiave con attivazione immediata in base alla [scadenza della chiave e](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) ai criteri in sequenza.

Il motivo per cui il sistema di protezione dei dati genera immediatamente una nuova chiave anziché eseguire il fallback a una chiave diversa consiste nel fatto che la nuova generazione di chiavi deve essere considerata come scadenza implicita di tutte le chiavi attivate prima della nuova chiave. L'idea generale è che le nuove chiavi potrebbero essere state configurate con algoritmi diversi o con meccanismi di crittografia inattivi rispetto alle chiavi obsolete e il sistema deve preferire la configurazione corrente sul fallback.

Si è verificata un'eccezione. Se lo sviluppatore dell'applicazione ha [disabilitato la generazione automatica delle chiavi](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), è necessario che il sistema di protezione dei dati scelga una chiave predefinita. In questo scenario di fallback, il sistema sceglie la chiave non revocata con la data di attivazione più recente, con la preferenza assegnata alle chiavi che hanno avuto tempo per la propagazione ad altri computer del cluster. Il sistema di fallback potrebbe quindi scegliere una chiave predefinita scaduta. Il sistema di fallback non sceglie mai una chiave revocata come chiave predefinita e se l'anello chiave è vuoto o ogni chiave è stata revocata, il sistema genera un errore durante l'inizializzazione.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>Scadenza e sequenza di chiavi

Quando viene creata una chiave, viene automaticamente fornita una data di attivazione di {Now + 2 giorni} e una data di scadenza di {Now + 90 giorni}. Il ritardo di 2 giorni prima dell'attivazione consente di propagarsi nel sistema. Ovvero consente ad altre applicazioni che puntano all'archivio di backup di osservare la chiave al successivo periodo di aggiornamento automatico, ottimizzando così le probabilità che, quando l'anello chiave diventa attivo, sia stato propagato a tutte le applicazioni che potrebbero dover utilizzarlo.

Se la chiave predefinita scadrà entro 2 giorni e se l'anello chiave non dispone già di una chiave che sarà attiva dopo la scadenza della chiave predefinita, il sistema di protezione dei dati renderà automaticamente persistente una nuova chiave per l'anello di chiave. La nuova chiave ha una data di attivazione di {data di scadenza della chiave predefinita} e una data di scadenza di {Now + 90 giorni}. Ciò consente al sistema di eseguire automaticamente il rollup delle chiavi a intervalli regolari senza interruzioni del servizio.

Potrebbero esserci casi in cui una chiave verrà creata con attivazione immediata. Un esempio potrebbe essere quando l'applicazione non è stata eseguita per un periodo di tempo e tutte le chiavi nell'anello di chiave sono scadute. Quando si verifica questa situazione, alla chiave viene assegnata una data di attivazione di {Now} senza il normale ritardo di attivazione di 2 giorni.

La durata predefinita della chiave è di 90 giorni, sebbene sia configurabile come nell'esempio seguente.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

Un amministratore può anche modificare l'impostazione predefinita a livello di sistema, anche se una chiamata esplicita a `SetDefaultKeyLifetime` eseguirà l'override di tutti i criteri a livello di sistema. La durata predefinita della chiave non può essere inferiore a 7 giorni.

## <a name="automatic-key-ring-refresh"></a>Aggiornamento automatico dell'anello chiave

Quando il sistema di protezione dei dati viene inizializzato, legge l'anello chiave dal repository sottostante e lo memorizza nella memoria. Questa cache consente alle operazioni di protezione e di rimozione della protezione di procedere senza toccare l'archivio di backup. Il sistema verificherà automaticamente l'archivio di backup per le modifiche approssimativamente ogni 24 ore o quando la chiave predefinita corrente scade, a seconda di quale sia la prima.

>[!WARNING]
> Gli sviluppatori dovrebbero molto raramente usare direttamente le API di gestione delle chiavi. Il sistema di protezione dei dati eseguirà la gestione automatica delle chiavi come descritto in precedenza.

Il sistema di protezione dei dati espone un'interfaccia `IKeyManager` che può essere usata per controllare e apportare modifiche all'anello di chiave. Il sistema DI DI che fornisce l'istanza di `IDataProtectionProvider` può inoltre fornire un'istanza di `IKeyManager` per l'utilizzo. In alternativa, è possibile effettuare il pull `IKeyManager` direttamente da `IServiceProvider` come nell'esempio riportato di seguito.

Qualsiasi operazione che modifica l'anello della chiave (creando una nuova chiave in modo esplicito o che esegue una revoca) invalida la cache in memoria. La chiamata successiva a `Protect` o `Unprotect` farà sì che il sistema di protezione dei dati legga nuovamente l'anello chiave e ricrea la cache.

Nell'esempio seguente viene illustrato l'utilizzo dell' `IKeyManager` interfaccia per esaminare e modificare l'anello chiave, inclusa la revoca delle chiavi esistenti e la generazione manuale di una nuova chiave.

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a>Archiviazione chiavi

Il sistema di protezione dei dati ha un'euristica che tenta di dedurre automaticamente un percorso di archiviazione chiavi appropriato e un meccanismo di crittografia inattivo. Il meccanismo di persistenza delle chiavi è anche configurabile dallo sviluppatore di app. I documenti seguenti illustrano le implementazioni predefinite di questi meccanismi:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
