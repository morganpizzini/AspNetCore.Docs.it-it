---
title: Considerazioni sulla sicurezza in gRPC per ASP.NET CoreSecurity considerations in gRPC for ASP.NET Core
author: jamesnk
description: Scopri di più sulle considerazioni sulla sicurezza per gRPC per ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: f84bec0ef485b701b2be36384a2e49b9b28e473d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667321"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="55343-103">Considerazioni sulla sicurezza in gRPC per ASP.NET CoreSecurity considerations in gRPC for ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55343-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="55343-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="55343-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="55343-105">In questo articolo vengono fornite informazioni sulla protezione di gRPC con .NET Core.This article provides information on securing gRPC with .NET Core.</span><span class="sxs-lookup"><span data-stu-id="55343-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="55343-106">Sicurezza del trasporto</span><span class="sxs-lookup"><span data-stu-id="55343-106">Transport security</span></span>

<span data-ttu-id="55343-107">I messaggi gRPC vengono inviati e ricevuti tramite HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="55343-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="55343-108">Si consiglia di:</span><span class="sxs-lookup"><span data-stu-id="55343-108">We recommend:</span></span>

* <span data-ttu-id="55343-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) da utilizzare per proteggere i messaggi nelle app gRPC di produzione.</span><span class="sxs-lookup"><span data-stu-id="55343-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="55343-110">I servizi gRPC devono solo ascoltare e rispondere tramite porte protette.</span><span class="sxs-lookup"><span data-stu-id="55343-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="55343-111">TLS è configurato in Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55343-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="55343-112">Per ulteriori informazioni sulla configurazione degli endpoint Kestrel, vedere [Configurazione degli endpoint Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="55343-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="55343-113">Eccezioni</span><span class="sxs-lookup"><span data-stu-id="55343-113">Exceptions</span></span>

<span data-ttu-id="55343-114">I messaggi di eccezione sono in genere considerati dati sensibili che non devono essere rivelati a un client.</span><span class="sxs-lookup"><span data-stu-id="55343-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="55343-115">Per impostazione predefinita, gRPC non invia al client i dettagli di un'eccezione generata da un servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="55343-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="55343-116">Al contrario, il client riceve un messaggio generico che indica che si è verificato un errore.</span><span class="sxs-lookup"><span data-stu-id="55343-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="55343-117">Il recapito dei messaggi di eccezione al client può essere sottoposto a override (ad esempio, in fase di sviluppo o test) con [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="55343-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="55343-118">I messaggi di eccezione non devono essere esposti al client nelle app di produzione.</span><span class="sxs-lookup"><span data-stu-id="55343-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="55343-119">Limiti di dimensione dei messaggi</span><span class="sxs-lookup"><span data-stu-id="55343-119">Message size limits</span></span>

<span data-ttu-id="55343-120">I messaggi in arrivo ai client e ai servizi gRPC vengono caricati in memoria.</span><span class="sxs-lookup"><span data-stu-id="55343-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="55343-121">I limiti di dimensione dei messaggi sono un meccanismo che consente di evitare che gRPC consumi un numero eccessivo di risorse.</span><span class="sxs-lookup"><span data-stu-id="55343-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="55343-122">gRPC utilizza i limiti di dimensione per messaggio per gestire i messaggi in arrivo e in uscita.</span><span class="sxs-lookup"><span data-stu-id="55343-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="55343-123">Per impostazione predefinita, gRPC limita i messaggi in arrivo a 4 MB.</span><span class="sxs-lookup"><span data-stu-id="55343-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="55343-124">Non vi è alcun limite ai messaggi in uscita.</span><span class="sxs-lookup"><span data-stu-id="55343-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="55343-125">Sul server, i limiti dei messaggi gRPC possono `AddGrpc`essere configurati per tutti i servizi in un'app con:</span><span class="sxs-lookup"><span data-stu-id="55343-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

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

<span data-ttu-id="55343-126">I limiti possono anche essere `AddServiceOptions<TService>`configurati per un singolo servizio utilizzando .</span><span class="sxs-lookup"><span data-stu-id="55343-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="55343-127">Per ulteriori informazioni sulla configurazione dei limiti di dimensione dei messaggi, vedere [configurazione gRPC](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="55343-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="55343-128">Convalida del certificato client</span><span class="sxs-lookup"><span data-stu-id="55343-128">Client certificate validation</span></span>

<span data-ttu-id="55343-129">[I certificati client](https://tools.ietf.org/html/rfc5246#section-7.4.4) vengono inizialmente convalidati quando viene stabilita la connessione.</span><span class="sxs-lookup"><span data-stu-id="55343-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="55343-130">Per impostazione predefinita, Kestrel non esegue la convalida aggiuntiva del certificato client di una connessione.</span><span class="sxs-lookup"><span data-stu-id="55343-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="55343-131">È consigliabile che i servizi gRPC protetti dai certificati client utilizzino il pacchetto [Microsoft.AspNetCore.Authentication.Certificate.](xref:security/authentication/certauth)</span><span class="sxs-lookup"><span data-stu-id="55343-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="55343-132">ASP.NET'autenticazione di certificazione di base eseguirà una convalida aggiuntiva su un certificato client, tra cui:ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span><span class="sxs-lookup"><span data-stu-id="55343-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="55343-133">Il certificato ha un utilizzo della chiave estesa (EKU) valido</span><span class="sxs-lookup"><span data-stu-id="55343-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="55343-134">È entro il suo periodo di validità</span><span class="sxs-lookup"><span data-stu-id="55343-134">Is within its validity period</span></span>
* <span data-ttu-id="55343-135">Controllare la revoca dei certificati</span><span class="sxs-lookup"><span data-stu-id="55343-135">Check certificate revocation</span></span>
