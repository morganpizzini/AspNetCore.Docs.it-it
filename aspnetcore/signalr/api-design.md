---
title: SignalR Considerazioni sulla progettazione di API
author: anurse
description: Informazioni su come progettare SignalR API per la compatibilità tra le versioni dell'app.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
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
uid: signalr/api-design
ms.openlocfilehash: 4a838c3a051476bd3d281e133d08b643656ae3b7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632902"
---
# <a name="no-locsignalr-api-design-considerations"></a>SignalR Considerazioni sulla progettazione di API

Di [Andrew Stanton-Nurse](https://twitter.com/anurse)

Questo articolo fornisce informazioni aggiuntive per la creazione di SignalR API basate su.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>Utilizzare parametri oggetto personalizzati per garantire la compatibilità con le versioni precedenti

L'aggiunta di parametri a un SignalR Metodo Hub (sul client o sul server) è una *modifica di rilievo*. Ciò significa che client/server meno recenti riceveranno errori quando tenteranno di richiamare il metodo senza il numero appropriato di parametri. Tuttavia, l'aggiunta di proprietà a un parametro di oggetto personalizzato **non** è una modifica sostanziale. Questo può essere usato per progettare API compatibili con resilienza alle modifiche nel client o nel server.

Si consideri, ad esempio, un'API sul lato server come la seguente:

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

Il client JavaScript chiama questo metodo usando `invoke` come indicato di seguito:

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

Se successivamente si aggiunge un secondo parametro al metodo Server, i client meno recenti non forniranno questo valore di parametro. Ad esempio:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

Quando il client precedente tenta di richiamare questo metodo, riceverà un errore simile al seguente:

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

Nel server verrà visualizzato un messaggio di log simile al seguente:

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

Il client precedente ha inviato un solo parametro, ma l'API del server più recente ha richiesto due parametri. L'utilizzo di oggetti personalizzati come parametri garantisce maggiore flessibilità. Riprogettare l'API originale per usare un oggetto personalizzato:

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

A questo punto, il client usa un oggetto per chiamare il metodo:

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

Anziché aggiungere un parametro, aggiungere una proprietà all' `TotalLengthRequest` oggetto:

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

Quando il client precedente Invia un singolo parametro, la proprietà aggiuntiva `Param2` verrà lasciata `null` . È possibile rilevare un messaggio inviato da un client precedente controllando `Param2` `null` e applicando un valore predefinito. Un nuovo client può inviare entrambi i parametri.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

La stessa tecnica funziona per i metodi definiti nel client. È possibile inviare un oggetto personalizzato dal lato server:

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

Sul lato client, è possibile accedere alla `Message` proprietà anziché usare un parametro:

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

Se successivamente si decide di aggiungere il mittente del messaggio al payload, aggiungere una proprietà all'oggetto:

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

I client meno recenti non sono in attesa del `Sender` valore, quindi verranno ignorati. Un nuovo client può accettarlo aggiornando per leggere la nuova proprietà:

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

In questo caso, anche il nuovo client è a tolleranza di un server obsoleto che non fornisce il `Sender` valore. Poiché il server precedente non fornirà il `Sender` valore, il client verificherà se esiste prima di accedervi.
