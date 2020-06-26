---
title: Gerarchia degli scopi e multi-tenant in ASP.NET Core
author: rick-anderson
description: Informazioni sulla gerarchia di stringhe per finalità e sul multi-tenant in relazione alle API di protezione dei dati ASP.NET Core.
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
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 8f069da500e7bc06e4b8712fbf7b86d90a815758
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404379"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>Gerarchia degli scopi e multi-tenant in ASP.NET Core

Poiché un oggetto `IDataProtector` è anche implicitamente un `IDataProtectionProvider` , gli scopi possono essere concatenati. In questo senso, `provider.CreateProtector([ "purpose1", "purpose2" ])` è equivalente a `provider.CreateProtector("purpose1").CreateProtector("purpose2")` .

Questo consente di eseguire alcune interessanti relazioni gerarchiche tramite il sistema di protezione dei dati. Nell'esempio precedente di [contoso. Messaging. SecureMessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose), il componente SecureMessage può chiamare `provider.CreateProtector("Contoso.Messaging.SecureMessage")` una volta in primo piano e memorizzare nella cache il risultato in un `_myProvider` campo privato. È quindi possibile creare protezioni future tramite chiamate a `_myProvider.CreateProtector("User: username")` e tali protezioni verranno utilizzate per proteggere i singoli messaggi.

Questa operazione può anche essere capovolta. Si consideri una singola applicazione logica che ospita più tenant (un CMS sembra ragionevole) e ogni tenant può essere configurato con il proprio sistema di gestione di autenticazione e stato. L'applicazione Umbrella dispone di un singolo provider master e chiama `provider.CreateProtector("Tenant 1")` e `provider.CreateProtector("Tenant 2")` per assegnare a ogni tenant una sezione isolata del sistema di protezione dei dati. I tenant possono quindi derivare le proprie protezioni personalizzate in base alle proprie esigenze, ma indipendentemente dal livello di difficoltà con cui tentano di non creare protezioni che si scontrano con qualsiasi altro tenant nel sistema. Graficamente, rappresentata come indicato di seguito.

![Scopi di multi-tenant](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> Si presuppone che l'applicazione Umbrella controlli quali API sono disponibili per i singoli tenant e che i tenant non possono eseguire codice arbitrario sul server. Se un tenant può eseguire codice arbitrario, può eseguire la reflection privata per suddividere le garanzie di isolamento oppure può semplicemente leggere direttamente il materiale della chiave master e derivare tutte le sottochiavi desiderate.

Il sistema di protezione dei dati utilizza effettivamente una sorta di multi-tenant nella configurazione predefinita. Per impostazione predefinita, il materiale per le chiavi master viene archiviato nella cartella del profilo utente dell'account del processo di lavoro (o nel registro di sistema per le identità del pool di applicazioni IIS). Ma in realtà è abbastanza comune usare un singolo account per eseguire più applicazioni, quindi tutte le applicazioni finiranno con la condivisione del materiale delle chiavi master. Per risolvere questo problema, il sistema di protezione dei dati inserisce automaticamente un identificatore univoco per applicazione come primo elemento nella catena di scopi generali. Questo scopo implicito consente di [isolare le singole applicazioni](xref:security/data-protection/configuration/overview#per-application-isolation) l'una dall'altra, trattando in modo efficace ogni applicazione come tenant univoco all'interno del sistema e il processo di creazione della protezione è identico all'immagine precedente.
