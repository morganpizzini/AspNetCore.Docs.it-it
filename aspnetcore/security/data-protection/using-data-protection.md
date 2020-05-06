---
title: Iniziare a usare le API di protezione dei dati in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare le API di protezione dei dati ASP.NET Core per la protezione e la rimozione della protezione dei dati in un'app.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: d5e9e61db39a67e8ccb7b345dfa4c97353312857
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774262"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>Iniziare a usare le API di protezione dei dati in ASP.NET Core

<a name="security-data-protection-getting-started"></a>

Al più semplice, la protezione dei dati è costituita dai passaggi seguenti:

1. Creare una protezione dati da un provider di protezione dati.

2. Chiamare il `Protect` metodo con i dati che si desidera proteggere.

3. Chiamare il `Unprotect` metodo con i dati di cui si vuole tornare in testo normale.

La maggior parte dei Framework e dei modelli di app, SignalRad esempio ASP.NET Core o, configura già il sistema di protezione dei dati e lo aggiunge a un contenitore di servizi a cui si accede tramite l'inserimento di dipendenze. Nell'esempio seguente viene illustrata la configurazione di un contenitore di servizi per l'inserimento di dipendenze e la registrazione dello stack DI protezione dei dati, la ricezione del provider DI protezione dati tramite DI, la creazione di un programma di protezione e la protezione dei dati.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Quando si crea una protezione, è necessario specificare una o più [stringhe per finalità](xref:security/data-protection/consumer-apis/purpose-strings). Una stringa di scopo fornisce l'isolamento tra i consumer. Ad esempio, una protezione creata con una stringa di scopo "verde" non sarà in grado di rimuovere la protezione dei dati forniti da un programma di protezione con scopo "viola".

>[!TIP]
> Le istanze `IDataProtectionProvider` di `IDataProtector` e sono thread-safe per più chiamanti. Si intende che quando un componente ottiene un riferimento a `IDataProtector` un tramite una chiamata a `CreateProtector`, utilizzerà tale riferimento per più chiamate a `Protect` e. `Unprotect`
>
>Una chiamata a `Unprotect` genererà CryptographicException se non è possibile verificare o decifrare il payload protetto. Alcuni componenti potrebbero voler ignorare gli errori durante le operazioni di rimozione della protezione. un componente che legge i cookie di autenticazione potrebbe gestire questo errore e trattare la richiesta come se non fosse disponibile alcun cookie anziché interrompere la richiesta in modo non corretto. I componenti che vogliono questo comportamento devono rilevare in modo specifico CryptographicException anziché ingoiare tutte le eccezioni.
