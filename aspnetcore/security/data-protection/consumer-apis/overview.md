---
title: Panoramica delle API consumer per ASP.NET Core
author: rick-anderson
description: Viene visualizzata una breve panoramica delle diverse API per i consumer disponibili nell'ASP.NET Core libreria di protezione dei dati.
ms.author: riande
ms.date: 06/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: e840111d702882a45e59cf89d679b87fa537d833
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767857"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>Panoramica delle API consumer per ASP.NET Core

Le `IDataProtectionProvider` interfacce `IDataProtector` e sono le interfacce di base attraverso le quali i consumer utilizzano il sistema di protezione dei dati. Si trovano nel pacchetto [Microsoft. AspNetCore. dataprotection. abstracts](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) .

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

L'interfaccia del provider rappresenta la radice del sistema di protezione dei dati. Non può essere usato direttamente per proteggere o rimuovere la protezione dei dati. Al contrario, il consumer deve ottenere un riferimento `IDataProtector` a un `IDataProtectionProvider.CreateProtector(purpose)`oggetto chiamando, dove scopo è una stringa che descrive il caso d'uso previsto del consumer. Per altre informazioni sullo scopo di questo parametro e su come scegliere un valore appropriato, vedere [stringhe di scopo](xref:security/data-protection/consumer-apis/purpose-strings) .

## <a name="idataprotector"></a>IDataProtector

L'interfaccia di protezione viene restituita da una chiamata `CreateProtector`a ed è questa interfaccia che i consumer possono utilizzare per eseguire operazioni di protezione e di rimozione della protezione.

Per proteggere una porzione di dati, passare i dati al `Protect` metodo. L'interfaccia di base definisce un metodo che converte Byte []-> byte [], ma esiste anche un overload (fornito come metodo di estensione) che converte la stringa > stringa. La sicurezza offerta dai due metodi è identica; lo sviluppatore deve scegliere qualsiasi overload più appropriato per il caso d'uso. Indipendentemente dall'overload scelto, il valore restituito dal metodo Protect è ora protetto (crittografato e a prova di manomissione) e l'applicazione può inviarlo a un client non attendibile.

Per rimuovere la protezione di un pezzo di dati protetto in precedenza, passare i dati protetti `Unprotect` al metodo. Sono disponibili overload basati su byte [] e basati su stringa per praticità degli sviluppatori. Se il payload protetto è stato generato da una precedente chiamata `Protect` a `IDataProtector`, il `Unprotect` metodo restituirà il payload originale non protetto. Se il payload protetto è stato manomesso o è stato prodotto da un `IDataProtector`diverso, `Unprotect` il metodo genererà CryptographicException.

Il concetto di confronto tra gli stessi `IDataProtector` e i diversi ritorni al concetto di scopo. Se due `IDataProtector` istanze sono state generate dalla stessa `IDataProtectionProvider` radice ma tramite stringhe di scopo diverse nella chiamata `IDataProtectionProvider.CreateProtector`a, vengono considerate [protezioni diverse](xref:security/data-protection/consumer-apis/purpose-strings)e non sarà possibile rimuovere la protezione dei payload generati dall'altro.

## <a name="consuming-these-interfaces"></a>Utilizzo di queste interfacce

Per un componente compatibile con, l'utilizzo previsto è che il componente accetta un `IDataProtectionProvider` parametro nel relativo costruttore e che il sistema di fornisce automaticamente questo servizio quando viene creata un'istanza del componente.

> [!NOTE]
> Alcune applicazioni, ad esempio le applicazioni console o le applicazioni ASP.NET 4. x, potrebbero non essere compatibili con, quindi non è possibile usare il meccanismo descritto DI seguito. Per questi scenari, vedere il documento sugli [scenari non compatibili](xref:security/data-protection/configuration/non-di-scenarios) per ulteriori informazioni su come ottenere un'istanza `IDataProtection` di un provider senza passare a.

Nell'esempio seguente vengono illustrati tre concetti:

1. [Aggiungere il sistema di protezione dei dati](xref:security/data-protection/configuration/overview) al contenitore del servizio

2. Utilizzo di per ricevere un'istanza di un `IDataProtectionProvider`oggetto e

3. Creazione di `IDataProtector` un oggetto `IDataProtectionProvider` da un oggetto e relativo utilizzo per proteggere e rimuovere la protezione dei dati.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Il pacchetto Microsoft. AspNetCore. dataprotection. abstracts contiene un metodo `IServiceProvider.GetDataProtector` di estensione come praticità per gli sviluppatori. Incapsula come singola operazione il recupero di un oggetto `IDataProtectionProvider` dal provider di servizi e la chiamata `IDataProtectionProvider.CreateProtector`a. Nell'esempio seguente viene illustrato l'utilizzo.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Le istanze `IDataProtectionProvider` di `IDataProtector` e sono thread-safe per più chiamanti. Si intende che quando un componente ottiene un riferimento a `IDataProtector` un tramite una chiamata a `CreateProtector`, utilizzerà tale riferimento per più chiamate a `Protect` e. `Unprotect` Una chiamata a `Unprotect` genererà CryptographicException se non è possibile verificare o decifrare il payload protetto. Alcuni componenti potrebbero voler ignorare gli errori durante le operazioni di rimozione della protezione. un componente che legge i cookie di autenticazione potrebbe gestire questo errore e trattare la richiesta come se non fosse disponibile alcun cookie anziché interrompere la richiesta in modo non corretto. I componenti che vogliono questo comportamento devono rilevare in modo specifico CryptographicException anziché ingoiare tutte le eccezioni.
