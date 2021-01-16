---
title: Considerazioni sulla sicurezza in gRPC per ASP.NET Core
author: jamesnk
description: Informazioni sulle considerazioni relative alla sicurezza per gRPC per ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: 45ac0916a368cf68f4d40e14298a7628446989ee
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252812"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="3b8a2-103">Considerazioni sulla sicurezza in gRPC per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b8a2-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="3b8a2-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="3b8a2-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="3b8a2-105">Questo articolo fornisce informazioni sulla protezione di gRPC con .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="3b8a2-106">Sicurezza del trasporto</span><span class="sxs-lookup"><span data-stu-id="3b8a2-106">Transport security</span></span>

<span data-ttu-id="3b8a2-107">i messaggi gRPC vengono inviati e ricevuti tramite HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="3b8a2-108">È consigliabile:</span><span class="sxs-lookup"><span data-stu-id="3b8a2-108">We recommend:</span></span>

* <span data-ttu-id="3b8a2-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) viene usato per proteggere i messaggi nelle app gRPC di produzione.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="3b8a2-110">i servizi gRPC devono restare in ascolto e rispondere solo su porte protette.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-110">gRPC services should only listen and respond over secured ports.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="3b8a2-111">TLS è configurato in gheppio.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="3b8a2-112">Per altre informazioni sulla configurazione degli endpoint di gheppio, vedere la pagina relativa alla [configurazione dell'endpoint gheppio](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="3b8a2-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>
::: moniker-end

::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="3b8a2-113">TLS è configurato in gheppio.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-113">TLS is configured in Kestrel.</span></span> <span data-ttu-id="3b8a2-114">Per altre informazioni sulla configurazione degli endpoint di gheppio, vedere la pagina relativa alla [configurazione dell'endpoint gheppio](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="3b8a2-114">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
::: moniker-end

## <a name="exceptions"></a><span data-ttu-id="3b8a2-115">Eccezioni</span><span class="sxs-lookup"><span data-stu-id="3b8a2-115">Exceptions</span></span>

<span data-ttu-id="3b8a2-116">I messaggi di eccezione vengono in genere considerati dati sensibili che non devono essere rivelati a un client.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-116">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="3b8a2-117">Per impostazione predefinita, gRPC non invia al client i dettagli di un'eccezione generata da un servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-117">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="3b8a2-118">Il client riceve invece un messaggio generico che indica che si è verificato un errore.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-118">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="3b8a2-119">Il recapito dei messaggi di eccezione al client può essere sottoposto a override (ad esempio, in fase di sviluppo o test) con [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="3b8a2-119">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="3b8a2-120">I messaggi di eccezione non devono essere esposti al client nelle app di produzione.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-120">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="3b8a2-121">Limiti delle dimensioni dei messaggi</span><span class="sxs-lookup"><span data-stu-id="3b8a2-121">Message size limits</span></span>

<span data-ttu-id="3b8a2-122">I messaggi in ingresso per i client e i servizi di gRPC vengono caricati in memoria.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-122">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="3b8a2-123">I limiti delle dimensioni dei messaggi sono un meccanismo che consente di impedire a gRPC di consumare risorse eccessive.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-123">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="3b8a2-124">gRPC utilizza i limiti delle dimensioni per messaggio per gestire i messaggi in ingresso e in uscita.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-124">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="3b8a2-125">Per impostazione predefinita, gRPC limita i messaggi in ingresso a 4 MB.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-125">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="3b8a2-126">Non esiste alcun limite per i messaggi in uscita.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-126">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="3b8a2-127">Sul server, è possibile configurare i limiti dei messaggi gRPC per tutti i servizi in un'app con `AddGrpc` :</span><span class="sxs-lookup"><span data-stu-id="3b8a2-127">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

<span data-ttu-id="3b8a2-128">È anche possibile configurare i limiti per un singolo servizio usando `AddServiceOptions<TService>` .</span><span class="sxs-lookup"><span data-stu-id="3b8a2-128">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="3b8a2-129">Per ulteriori informazioni sulla configurazione dei limiti delle dimensioni dei messaggi, vedere [configurazione di gRPC](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="3b8a2-129">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="3b8a2-130">Convalida del certificato client</span><span class="sxs-lookup"><span data-stu-id="3b8a2-130">Client certificate validation</span></span>

<span data-ttu-id="3b8a2-131">I [certificati client](https://tools.ietf.org/html/rfc5246#section-7.4.4) vengono inizialmente convalidati quando viene stabilita la connessione.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-131">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="3b8a2-132">Per impostazione predefinita, gheppio non esegue la convalida aggiuntiva del certificato client di una connessione.</span><span class="sxs-lookup"><span data-stu-id="3b8a2-132">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="3b8a2-133">È consigliabile che i servizi gRPC protetti dai certificati client usino il pacchetto [Microsoft. AspNetCore. Authentication. Certificate](xref:security/authentication/certauth) .</span><span class="sxs-lookup"><span data-stu-id="3b8a2-133">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="3b8a2-134">ASP.NET Core Autenticazione della certificazione eseguirà una convalida aggiuntiva per un certificato client, tra cui:</span><span class="sxs-lookup"><span data-stu-id="3b8a2-134">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="3b8a2-135">Il certificato ha un utilizzo chiave esteso valido (EKU)</span><span class="sxs-lookup"><span data-stu-id="3b8a2-135">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="3b8a2-136">Rientra nel periodo di validità</span><span class="sxs-lookup"><span data-stu-id="3b8a2-136">Is within its validity period</span></span>
* <span data-ttu-id="3b8a2-137">Controllare la revoca dei certificati</span><span class="sxs-lookup"><span data-stu-id="3b8a2-137">Check certificate revocation</span></span>
