---
title: Gestire utenti e gruppi inSignalR
author: bradygaster
description: Panoramica di ASP.NET Core SignalR gestione di utenti e gruppi.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: af498575899fcfa407aba9f9f49c0bfeabadc7a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776298"
---
# <a name="manage-users-and-groups-in-signalr"></a>Gestire utenti e gruppi inSignalR

Di [Brennan Conroy](https://github.com/BrennanConroy)

SignalRconsente l'invio di messaggi a tutte le connessioni associate a un utente specifico, oltre che ai gruppi denominati di connessioni.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(procedura per il download)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>Utenti inSignalR

SignalRconsente di inviare messaggi a tutte le connessioni associate a un utente specifico. Per impostazione predefinita SignalR , utilizza `ClaimTypes.NameIdentifier` l'oggetto `ClaimsPrincipal` dall'oggetto associato alla connessione come identificatore utente. Un singolo utente può avere più connessioni a un' SignalR app. Ad esempio, un utente potrebbe essere connesso sul desktop e sul telefono. Ogni dispositivo ha una connessione SignalR separata, ma tutti sono associati allo stesso utente. Se un messaggio viene inviato all'utente, tutte le connessioni associate a tale utente riceveranno il messaggio. È possibile accedere all'identificatore utente per una connessione tramite la `Context.UserIdentifier` proprietà nell'hub.

Inviare un messaggio a un utente specifico passando l'identificatore utente alla `User` funzione nel metodo dell'hub, come illustrato nell'esempio seguente:

> [!NOTE]
> L'identificatore utente distingue tra maiuscole e minuscole.

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>Gruppi inSignalR

Un gruppo è una raccolta di connessioni associate a un nome. I messaggi possono essere inviati a tutte le connessioni in un gruppo. I gruppi sono il metodo consigliato per l'invio a una connessione o a più connessioni perché i gruppi sono gestiti dall'applicazione. Una connessione può essere un membro di più gruppi. In questo modo i gruppi sono ideali per un elemento come un'applicazione di chat, in cui ogni stanza può essere rappresentata come un gruppo. Le connessioni possono essere aggiunte o rimosse dai gruppi tramite `AddToGroupAsync` i `RemoveFromGroupAsync` metodi e.

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

L'appartenenza al gruppo non viene mantenuta quando si riconnette una connessione. La connessione deve essere nuovamente aggiunta al gruppo quando viene ristabilita. Non è possibile contare i membri di un gruppo, poiché queste informazioni non sono disponibili se l'applicazione viene ridimensionata in più server.

Per proteggere l'accesso alle risorse durante l'uso di gruppi, usare la funzionalità di [autenticazione e autorizzazione](xref:signalr/authn-and-authz) in ASP.NET Core. Se si aggiungono utenti a un gruppo solo quando le credenziali sono valide per il gruppo, i messaggi inviati a tale gruppo passeranno solo agli utenti autorizzati. Tuttavia, i gruppi non sono una funzionalità di sicurezza. Le attestazioni di autenticazione hanno funzionalità che non vengono eseguite dai gruppi, ad esempio la scadenza e la revoca. Se l'autorizzazione dell'utente per accedere al gruppo viene revocata, è necessario rilevarla manualmente e rimuoverle dal gruppo.

> [!NOTE]
> I nomi dei gruppi fanno distinzione tra maiuscole e minuscole

## <a name="related-resources"></a>Risorse correlate

* [Operazioni preliminari](xref:tutorials/signalr)
* [Hub](xref:signalr/hubs)
* [Pubblicazione in Azure](xref:signalr/publish-to-azure-web-app)
