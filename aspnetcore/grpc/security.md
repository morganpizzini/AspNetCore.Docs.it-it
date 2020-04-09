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
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>Considerazioni sulla sicurezza in gRPC per ASP.NET CoreSecurity considerations in gRPC for ASP.NET Core

Di [James Newton-King](https://twitter.com/jamesnk)

In questo articolo vengono fornite informazioni sulla protezione di gRPC con .NET Core.This article provides information on securing gRPC with .NET Core.

## <a name="transport-security"></a>Sicurezza del trasporto

I messaggi gRPC vengono inviati e ricevuti tramite HTTP/2. Si consiglia di:

* [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) da utilizzare per proteggere i messaggi nelle app gRPC di produzione.
* I servizi gRPC devono solo ascoltare e rispondere tramite porte protette.

TLS è configurato in Kestrel. Per ulteriori informazioni sulla configurazione degli endpoint Kestrel, vedere [Configurazione degli endpoint Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

## <a name="exceptions"></a>Eccezioni

I messaggi di eccezione sono in genere considerati dati sensibili che non devono essere rivelati a un client. Per impostazione predefinita, gRPC non invia al client i dettagli di un'eccezione generata da un servizio gRPC. Al contrario, il client riceve un messaggio generico che indica che si è verificato un errore. Il recapito dei messaggi di eccezione al client può essere sottoposto a override (ad esempio, in fase di sviluppo o test) con [EnableDetailedErrors](xref:grpc/configuration#configure-services-options). I messaggi di eccezione non devono essere esposti al client nelle app di produzione.

## <a name="message-size-limits"></a>Limiti di dimensione dei messaggi

I messaggi in arrivo ai client e ai servizi gRPC vengono caricati in memoria. I limiti di dimensione dei messaggi sono un meccanismo che consente di evitare che gRPC consumi un numero eccessivo di risorse.

gRPC utilizza i limiti di dimensione per messaggio per gestire i messaggi in arrivo e in uscita. Per impostazione predefinita, gRPC limita i messaggi in arrivo a 4 MB. Non vi è alcun limite ai messaggi in uscita.

Sul server, i limiti dei messaggi gRPC possono `AddGrpc`essere configurati per tutti i servizi in un'app con:

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

I limiti possono anche essere `AddServiceOptions<TService>`configurati per un singolo servizio utilizzando . Per ulteriori informazioni sulla configurazione dei limiti di dimensione dei messaggi, vedere [configurazione gRPC](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>Convalida del certificato client

[I certificati client](https://tools.ietf.org/html/rfc5246#section-7.4.4) vengono inizialmente convalidati quando viene stabilita la connessione. Per impostazione predefinita, Kestrel non esegue la convalida aggiuntiva del certificato client di una connessione.

È consigliabile che i servizi gRPC protetti dai certificati client utilizzino il pacchetto [Microsoft.AspNetCore.Authentication.Certificate.](xref:security/authentication/certauth) ASP.NET'autenticazione di certificazione di base eseguirà una convalida aggiuntiva su un certificato client, tra cui:ASP.NET Core certification authentication will perform additional validation on a client certificate, including:

* Il certificato ha un utilizzo della chiave estesa (EKU) valido
* È entro il suo periodo di validità
* Controllare la revoca dei certificati
