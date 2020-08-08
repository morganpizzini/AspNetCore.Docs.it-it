---
title: SignalRConsiderazioni sulla progettazione di API
author: anurse
description: Informazioni su come progettare SignalR API per la compatibilità tra le versioni dell'app.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
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
ms.openlocfilehash: ef0285c611bd41d7fe686a4b370b6daae9be9174
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018988"
---
# <a name="no-locsignalr-api-design-considerations"></a><span data-ttu-id="169b8-103">SignalRConsiderazioni sulla progettazione di API</span><span class="sxs-lookup"><span data-stu-id="169b8-103">SignalR API design considerations</span></span>

<span data-ttu-id="169b8-104">Di [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="169b8-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="169b8-105">Questo articolo fornisce informazioni aggiuntive per la creazione di SignalR API basate su.</span><span class="sxs-lookup"><span data-stu-id="169b8-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="169b8-106">Utilizzare parametri oggetto personalizzati per garantire la compatibilità con le versioni precedenti</span><span class="sxs-lookup"><span data-stu-id="169b8-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="169b8-107">L'aggiunta di parametri a un SignalR Metodo Hub (sul client o sul server) è una *modifica di rilievo*.</span><span class="sxs-lookup"><span data-stu-id="169b8-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="169b8-108">Ciò significa che client/server meno recenti riceveranno errori quando tenteranno di richiamare il metodo senza il numero appropriato di parametri.</span><span class="sxs-lookup"><span data-stu-id="169b8-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="169b8-109">Tuttavia, l'aggiunta di proprietà a un parametro di oggetto personalizzato **non** è una modifica sostanziale.</span><span class="sxs-lookup"><span data-stu-id="169b8-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="169b8-110">Questo può essere usato per progettare API compatibili con resilienza alle modifiche nel client o nel server.</span><span class="sxs-lookup"><span data-stu-id="169b8-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="169b8-111">Si consideri, ad esempio, un'API sul lato server come la seguente:</span><span class="sxs-lookup"><span data-stu-id="169b8-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="169b8-112">Il client JavaScript chiama questo metodo usando `invoke` come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="169b8-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="169b8-113">Se successivamente si aggiunge un secondo parametro al metodo Server, i client meno recenti non forniranno questo valore di parametro.</span><span class="sxs-lookup"><span data-stu-id="169b8-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="169b8-114">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="169b8-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="169b8-115">Quando il client precedente tenta di richiamare questo metodo, riceverà un errore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="169b8-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="169b8-116">Nel server verrà visualizzato un messaggio di log simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="169b8-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="169b8-117">Il client precedente ha inviato un solo parametro, ma l'API del server più recente ha richiesto due parametri.</span><span class="sxs-lookup"><span data-stu-id="169b8-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="169b8-118">L'utilizzo di oggetti personalizzati come parametri garantisce maggiore flessibilità.</span><span class="sxs-lookup"><span data-stu-id="169b8-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="169b8-119">Riprogettare l'API originale per usare un oggetto personalizzato:</span><span class="sxs-lookup"><span data-stu-id="169b8-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="169b8-120">A questo punto, il client usa un oggetto per chiamare il metodo:</span><span class="sxs-lookup"><span data-stu-id="169b8-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="169b8-121">Anziché aggiungere un parametro, aggiungere una proprietà all' `TotalLengthRequest` oggetto:</span><span class="sxs-lookup"><span data-stu-id="169b8-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="169b8-122">Quando il client precedente Invia un singolo parametro, la proprietà aggiuntiva `Param2` verrà lasciata `null` .</span><span class="sxs-lookup"><span data-stu-id="169b8-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="169b8-123">È possibile rilevare un messaggio inviato da un client precedente controllando `Param2` `null` e applicando un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="169b8-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="169b8-124">Un nuovo client può inviare entrambi i parametri.</span><span class="sxs-lookup"><span data-stu-id="169b8-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="169b8-125">La stessa tecnica funziona per i metodi definiti nel client.</span><span class="sxs-lookup"><span data-stu-id="169b8-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="169b8-126">È possibile inviare un oggetto personalizzato dal lato server:</span><span class="sxs-lookup"><span data-stu-id="169b8-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="169b8-127">Sul lato client, è possibile accedere alla `Message` proprietà anziché usare un parametro:</span><span class="sxs-lookup"><span data-stu-id="169b8-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="169b8-128">Se successivamente si decide di aggiungere il mittente del messaggio al payload, aggiungere una proprietà all'oggetto:</span><span class="sxs-lookup"><span data-stu-id="169b8-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="169b8-129">I client meno recenti non sono in attesa del `Sender` valore, quindi verranno ignorati.</span><span class="sxs-lookup"><span data-stu-id="169b8-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="169b8-130">Un nuovo client può accettarlo aggiornando per leggere la nuova proprietà:</span><span class="sxs-lookup"><span data-stu-id="169b8-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="169b8-131">In questo caso, anche il nuovo client è a tolleranza di un server obsoleto che non fornisce il `Sender` valore.</span><span class="sxs-lookup"><span data-stu-id="169b8-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="169b8-132">Poiché il server precedente non fornirà il `Sender` valore, il client verificherà se esiste prima di accedervi.</span><span class="sxs-lookup"><span data-stu-id="169b8-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
