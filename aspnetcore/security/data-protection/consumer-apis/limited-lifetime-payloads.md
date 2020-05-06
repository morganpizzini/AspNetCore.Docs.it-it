---
title: Limitare la durata dei payload protetti in ASP.NET Core
author: rick-anderson
description: Informazioni su come limitare la durata di un payload protetto usando le API di protezione dei dati ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: bc1597f75d8c5f786d46e59ac027d01ffca077c0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768611"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>Limitare la durata dei payload protetti in ASP.NET Core

Esistono scenari in cui lo sviluppatore di applicazioni desidera creare un payload protetto che scada dopo un determinato periodo di tempo. Ad esempio, il payload protetto potrebbe rappresentare un token di reimpostazione della password che dovrebbe essere valido solo per un'ora. È certamente possibile che lo sviluppatore crei un formato di payload che contenga una data di scadenza incorporata e che gli sviluppatori avanzati vogliano comunque eseguire questa operazione, ma per la maggior parte degli sviluppatori che gestiscono queste scadenze può diventare noioso.

Per semplificare questa operazione per i destinatari degli sviluppatori, il pacchetto [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contiene API di utilità per la creazione di payload che scadono automaticamente dopo un determinato periodo di tempo. Queste API si bloccano dal `ITimeLimitedDataProtector` tipo.

## <a name="api-usage"></a>Utilizzo API

L' `ITimeLimitedDataProtector` interfaccia è l'interfaccia di base per la protezione e la rimozione della protezione dei payload a tempo limitato o a scadenza automatica. Per creare un'istanza di un `ITimeLimitedDataProtector`, è necessario innanzitutto un'istanza di un [IDataProtector](xref:security/data-protection/consumer-apis/overview) normale costruito con uno scopo specifico. Quando l' `IDataProtector` istanza è disponibile, chiamare il `IDataProtector.ToTimeLimitedDataProtector` metodo di estensione per ottenere una protezione con le funzionalità di scadenza predefinite.

`ITimeLimitedDataProtector`espone i seguenti metodi di estensione e superficie API:

* CreateProtector (scopo della stringa): ITimeLimitedDataProtector-questa API è simile a quella `IDataProtectionProvider.CreateProtector` esistente perché può essere usata per creare [catene di scopi](xref:security/data-protection/consumer-apis/purpose-strings) da un programma di protezione con limitazioni temporali radice.

* Protect (byte [] testo normale, scadenza DateTimeOffset): byte []

* Protect (byte [] testo non crittografato, durata TimeSpan): byte []

* Protect (byte [] testo normale): byte []

* Protect (testo non crittografato, scadenza DateTimeOffset): stringa

* Protect (testo non crittografato, durata TimeSpan): stringa

* Protect (testo non crittografato): stringa

Oltre ai metodi principali `Protect` che accettano solo il testo non crittografato, sono disponibili nuovi overload che consentono di specificare la data di scadenza del payload. La data di scadenza può essere specificata come una data assoluta (tramite `DateTimeOffset`un) o come ora relativa (dall'ora di sistema corrente, tramite `TimeSpan`). Se viene chiamato un overload che non accetta una scadenza, il payload viene considerato mai scaduto.

* Unprotect (byte [] protectedData, out DateTimeOffset scadenza): byte []

* Unprotect (byte [] protectedData): byte []

* Unprotect (String protectedData, out DateTimeOffset scadenza): stringa

* Unprotect (String protectedData): stringa

I `Unprotect` metodi restituiscono i dati originali non protetti. Se il payload non è ancora scaduto, la scadenza assoluta viene restituita come parametro out facoltativo insieme ai dati non protetti originali. Se il payload è scaduto, tutti gli overload del metodo Unprotect genereranno CryptographicException.

>[!WARNING]
> Non è consigliabile usare queste API per proteggere i payload che richiedono persistenza a lungo termine o indefinito. "È possibile garantire che i payload protetti siano irreversibili in modo permanente dopo un mese?" può fungere da regola generale. Se la risposta è No, gli sviluppatori devono prendere in considerazione API alternative.

Nell'esempio seguente vengono usati i [percorsi di codice non di](xref:security/data-protection/configuration/non-di-scenarios) per la creazione di un'istanza del sistema di protezione dei dati. Per eseguire questo esempio, verificare di aver prima aggiunto un riferimento al pacchetto Microsoft. AspNetCore. dataprotection. Extensions.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
