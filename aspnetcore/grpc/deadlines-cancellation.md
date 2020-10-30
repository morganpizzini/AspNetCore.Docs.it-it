---
title: Servizi gRPC affidabili con scadenze e annullamento
author: jamesnk
description: Informazioni su come creare servizi gRPC affidabili con scadenze e annullamento in .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059923"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a><span data-ttu-id="274d8-103">Servizi gRPC affidabili con scadenze e annullamento</span><span class="sxs-lookup"><span data-stu-id="274d8-103">Reliable gRPC services with deadlines and cancellation</span></span>

<span data-ttu-id="274d8-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="274d8-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="274d8-105">Le scadenze e l'annullamento sono funzionalità usate dai client gRPC per interrompere le chiamate in corso.</span><span class="sxs-lookup"><span data-stu-id="274d8-105">Deadlines and cancellation are features used by gRPC clients to abort in-progress calls.</span></span> <span data-ttu-id="274d8-106">Questo articolo illustra il motivo per cui le scadenze e l'annullamento sono importanti e come usarle nelle app gRPC .NET.</span><span class="sxs-lookup"><span data-stu-id="274d8-106">This article discusses why deadlines and cancellation are important, and how to use them in .NET gRPC apps.</span></span>

## <a name="deadlines"></a><span data-ttu-id="274d8-107">Scadenze</span><span class="sxs-lookup"><span data-stu-id="274d8-107">Deadlines</span></span>

<span data-ttu-id="274d8-108">Una scadenza consente a un client gRPC di specificare il tempo di attesa per il completamento di una chiamata.</span><span class="sxs-lookup"><span data-stu-id="274d8-108">A deadline allows a gRPC client to specify how long it will wait for a call to complete.</span></span> <span data-ttu-id="274d8-109">Quando viene superata una scadenza, la chiamata viene annullata.</span><span class="sxs-lookup"><span data-stu-id="274d8-109">When a deadline is exceeded, the call is canceled.</span></span> <span data-ttu-id="274d8-110">L'impostazione di una scadenza è importante perché fornisce un limite superiore per il periodo di tempo per cui è possibile eseguire una chiamata.</span><span class="sxs-lookup"><span data-stu-id="274d8-110">Setting a deadline is important because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="274d8-111">Si interrompe l'esecuzione di servizi non confunzionanti e l'esaurimento delle risorse del server.</span><span class="sxs-lookup"><span data-stu-id="274d8-111">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="274d8-112">Le scadenze sono uno strumento utile per la creazione di app affidabili ed è necessario configurarle.</span><span class="sxs-lookup"><span data-stu-id="274d8-112">Deadlines are a useful tool for building reliable apps and should be configured.</span></span>

<span data-ttu-id="274d8-113">Configurazione scadenza:</span><span class="sxs-lookup"><span data-stu-id="274d8-113">Deadline configuration:</span></span>

* <span data-ttu-id="274d8-114">Una scadenza viene configurata utilizzando `CallOptions.Deadline` quando viene effettuata una chiamata.</span><span class="sxs-lookup"><span data-stu-id="274d8-114">A deadline is configured using `CallOptions.Deadline` when a call is made.</span></span>
* <span data-ttu-id="274d8-115">Non esiste alcun valore di scadenza predefinito.</span><span class="sxs-lookup"><span data-stu-id="274d8-115">There is no default deadline value.</span></span> <span data-ttu-id="274d8-116">le chiamate gRPC non sono limitate all'ora, a meno che non venga specificata una scadenza.</span><span class="sxs-lookup"><span data-stu-id="274d8-116">gRPC calls aren't time limited unless a deadline is specified.</span></span>
* <span data-ttu-id="274d8-117">Una scadenza è l'ora UTC di quando viene superata la scadenza.</span><span class="sxs-lookup"><span data-stu-id="274d8-117">A deadline is the UTC time of when the deadline is exceeded.</span></span> <span data-ttu-id="274d8-118">Ad esempio, `DateTime.UtcNow.AddSeconds(5)` è una scadenza di 5 secondi a partire da ora.</span><span class="sxs-lookup"><span data-stu-id="274d8-118">For example, `DateTime.UtcNow.AddSeconds(5)` is a deadline of 5 seconds from now.</span></span>
* <span data-ttu-id="274d8-119">Se viene usata un'ora passata o corrente, la chiamata supera immediatamente la scadenza.</span><span class="sxs-lookup"><span data-stu-id="274d8-119">If a past or current time is used then the call immediately exceeds the deadline.</span></span>
* <span data-ttu-id="274d8-120">La scadenza viene inviata con la chiamata gRPC al servizio e viene rilevata indipendentemente dal client e dal servizio.</span><span class="sxs-lookup"><span data-stu-id="274d8-120">The deadline is sent with the gRPC call to the service and is independently tracked by both the client and the service.</span></span> <span data-ttu-id="274d8-121">È possibile che una chiamata gRPC venga completata in un computer, ma nel momento in cui la risposta è stata restituita al client la scadenza è stata superata.</span><span class="sxs-lookup"><span data-stu-id="274d8-121">It is possible that a gRPC call completes on one machine, but by the time the response has returned to the client the deadline has been exceeded.</span></span>

<span data-ttu-id="274d8-122">Se viene superata una scadenza, il client e il servizio hanno un comportamento diverso:</span><span class="sxs-lookup"><span data-stu-id="274d8-122">If a deadline is exceeded, the client and service have different behavior:</span></span>

* <span data-ttu-id="274d8-123">Il client interrompe immediatamente la richiesta HTTP sottostante e genera un `DeadlineExceeded` errore.</span><span class="sxs-lookup"><span data-stu-id="274d8-123">The client immediately aborts the underlying HTTP request and throws a `DeadlineExceeded` error.</span></span> <span data-ttu-id="274d8-124">L'app client può scegliere di intercettare l'errore e visualizzare un messaggio di timeout per l'utente.</span><span class="sxs-lookup"><span data-stu-id="274d8-124">The client app can choose to catch the error and display a timeout message to the user.</span></span>
* <span data-ttu-id="274d8-125">Sul server, la richiesta HTTP in esecuzione viene interrotta e viene generato [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) .</span><span class="sxs-lookup"><span data-stu-id="274d8-125">On the server, the executing HTTP request is aborted and [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="274d8-126">Sebbene la richiesta HTTP venga interrotta, la chiamata gRPC continua a essere eseguita nel server fino al completamento del metodo.</span><span class="sxs-lookup"><span data-stu-id="274d8-126">Although the HTTP request is aborted, the gRPC call continues to run on the server until the method completes.</span></span> <span data-ttu-id="274d8-127">È importante che il token di annullamento venga passato ai metodi asincroni, in modo che vengano annullati insieme alla chiamata.</span><span class="sxs-lookup"><span data-stu-id="274d8-127">It's important that the cancellation token is passed to async methods so they are cancelled along with the call.</span></span> <span data-ttu-id="274d8-128">Ad esempio, passando un token di annullamento a query asincrone del database e richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="274d8-128">For example, passing a cancellation token to async database queries and HTTP requests.</span></span> <span data-ttu-id="274d8-129">Il passaggio di un token di annullamento consente di completare rapidamente la chiamata annullata sul server e liberare risorse per altre chiamate.</span><span class="sxs-lookup"><span data-stu-id="274d8-129">Passing a cancellation token allows the canceled call to complete quickly on the server and free up resources for other calls.</span></span>

<span data-ttu-id="274d8-130">Configurare `CallOptions.Deadline` per impostare una scadenza per una chiamata gRPC:</span><span class="sxs-lookup"><span data-stu-id="274d8-130">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="274d8-131">Uso `ServerCallContext.CancellationToken` di in un servizio gRPC:</span><span class="sxs-lookup"><span data-stu-id="274d8-131">Using `ServerCallContext.CancellationToken` in a gRPC service:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a><span data-ttu-id="274d8-132">Scadenze propagate</span><span class="sxs-lookup"><span data-stu-id="274d8-132">Propagating deadlines</span></span>

<span data-ttu-id="274d8-133">Quando viene effettuata una chiamata gRPC da un servizio gRPC in esecuzione, la scadenza deve essere propagata.</span><span class="sxs-lookup"><span data-stu-id="274d8-133">When a gRPC call is made from an executing gRPC service, the deadline should be propagated.</span></span> <span data-ttu-id="274d8-134">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="274d8-134">For example:</span></span>

1. <span data-ttu-id="274d8-135">L'app client chiama `FrontendService.GetUser` con una scadenza.</span><span class="sxs-lookup"><span data-stu-id="274d8-135">Client app calls `FrontendService.GetUser` with a deadline.</span></span>
2. <span data-ttu-id="274d8-136">`FrontendService` chiama `UserService.GetUser`.</span><span class="sxs-lookup"><span data-stu-id="274d8-136">`FrontendService` calls `UserService.GetUser`.</span></span> <span data-ttu-id="274d8-137">La scadenza specificata dal client deve essere specificata con la nuova chiamata a gRPC.</span><span class="sxs-lookup"><span data-stu-id="274d8-137">The deadline specified by the client should be specified with the new gRPC call.</span></span>
3. <span data-ttu-id="274d8-138">`UserService.GetUser` riceve la scadenza.</span><span class="sxs-lookup"><span data-stu-id="274d8-138">`UserService.GetUser` receives the deadline.</span></span> <span data-ttu-id="274d8-139">Si verifica un timeout corretto se viene superata la scadenza dell'app client.</span><span class="sxs-lookup"><span data-stu-id="274d8-139">It correctly times-out if the client app's deadline is exceeded.</span></span>

<span data-ttu-id="274d8-140">Il contesto di chiamata fornisce la scadenza con `ServerCallContext.Deadline` :</span><span class="sxs-lookup"><span data-stu-id="274d8-140">The call context provides the deadline with `ServerCallContext.Deadline`:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

<span data-ttu-id="274d8-141">La propagazione manuale delle scadenze può essere complessa.</span><span class="sxs-lookup"><span data-stu-id="274d8-141">Manually propagating deadlines can be cumbersome.</span></span> <span data-ttu-id="274d8-142">La scadenza deve essere passata a ogni chiamata ed è facile da perdere accidentalmente.</span><span class="sxs-lookup"><span data-stu-id="274d8-142">The deadline needs to be passed to every call, and it's easy to accidentally miss.</span></span> <span data-ttu-id="274d8-143">Una soluzione automatica è disponibile con gRPC client Factory.</span><span class="sxs-lookup"><span data-stu-id="274d8-143">An automatic solution is available with gRPC client factory.</span></span> <span data-ttu-id="274d8-144">Specifica `EnableCallContextPropagation` :</span><span class="sxs-lookup"><span data-stu-id="274d8-144">Specifying `EnableCallContextPropagation`:</span></span>

* <span data-ttu-id="274d8-145">Propaga automaticamente la scadenza e il token di annullamento alle chiamate figlio.</span><span class="sxs-lookup"><span data-stu-id="274d8-145">Automatically propagates the deadline and cancellation token to child calls.</span></span>
* <span data-ttu-id="274d8-146">È un ottimo modo per garantire che gli scenari gRPC complessi e nidificati propaghino sempre la scadenza e l'annullamento.</span><span class="sxs-lookup"><span data-stu-id="274d8-146">Is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

<span data-ttu-id="274d8-147">Per altre informazioni, vedere <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span><span class="sxs-lookup"><span data-stu-id="274d8-147">For more information, see <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span></span>

## <a name="cancellation"></a><span data-ttu-id="274d8-148">Annullamento</span><span class="sxs-lookup"><span data-stu-id="274d8-148">Cancellation</span></span>

<span data-ttu-id="274d8-149">L'annullamento consente a un client gRPC di annullare chiamate con esecuzione prolungata che non sono più necessarie.</span><span class="sxs-lookup"><span data-stu-id="274d8-149">Cancellation allows a gRPC client to cancel long running calls that are no longer needed.</span></span> <span data-ttu-id="274d8-150">Ad esempio, una chiamata gRPC che trasmette gli aggiornamenti in tempo reale viene avviata quando l'utente visita una pagina in un sito Web.</span><span class="sxs-lookup"><span data-stu-id="274d8-150">For example, a gRPC call that streams realtime updates is started when the user visits a page on a website.</span></span> <span data-ttu-id="274d8-151">Il flusso deve essere annullato quando l'utente si sposta da una pagina all'altra.</span><span class="sxs-lookup"><span data-stu-id="274d8-151">The stream should be canceled when the user navigates away from the page.</span></span>

<span data-ttu-id="274d8-152">Una chiamata gRPC può essere annullata nel client passando un token di annullamento con [CallOptions. CancellationToken](xref:System.Threading.CancellationToken) o chiamando `Dispose` sulla chiamata.</span><span class="sxs-lookup"><span data-stu-id="274d8-152">A gRPC call can be canceled in the client by passing a cancellation token with [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) or calling `Dispose` on the call.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

<span data-ttu-id="274d8-153">i servizi gRPC che possono essere annullati devono:</span><span class="sxs-lookup"><span data-stu-id="274d8-153">gRPC services that can be cancelled should:</span></span>
* <span data-ttu-id="274d8-154">Passare `ServerCallContext.CancellationToken` a metodi asincroni.</span><span class="sxs-lookup"><span data-stu-id="274d8-154">Pass `ServerCallContext.CancellationToken` to async methods.</span></span> <span data-ttu-id="274d8-155">L'annullamento di metodi asincroni consente il completamento rapido della chiamata sul server.</span><span class="sxs-lookup"><span data-stu-id="274d8-155">Canceling async methods allows the call on the server to complete quickly.</span></span>
* <span data-ttu-id="274d8-156">Propaga il token di annullamento alle chiamate figlio.</span><span class="sxs-lookup"><span data-stu-id="274d8-156">Propagate the cancellation token to child calls.</span></span> <span data-ttu-id="274d8-157">La propagazione del token di annullamento garantisce che le chiamate figlio vengano annullate con il relativo elemento padre.</span><span class="sxs-lookup"><span data-stu-id="274d8-157">Propagating the cancellation token ensures that child calls are canceled with their parent.</span></span> <span data-ttu-id="274d8-158">[gRPC client Factory](xref:grpc/clientfactory) e `EnableCallContextPropagation()` propaga automaticamente il token di annullamento.</span><span class="sxs-lookup"><span data-stu-id="274d8-158">[gRPC client factory](xref:grpc/clientfactory) and `EnableCallContextPropagation()` automatically propagates the cancellation token.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="274d8-159">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="274d8-159">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/clientfactory>
