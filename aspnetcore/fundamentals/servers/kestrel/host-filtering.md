---
title: Filtro host con server Web ASP.NET Core gheppio
author: rick-anderson
description: Informazioni sull'uso del filtro host con gheppio, il server Web multipiattaforma per ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/host-filtering
ms.openlocfilehash: d55c211f05a77f6acabedef2ff62a621d9a1844e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253993"
---
# <a name="host-filtering-with-aspnet-core-kestrel-web-server"></a>Filtro host con server Web ASP.NET Core gheppio

Mentre supporta la configurazione in base ai prefissi, ad esempio `http://example.com:5000`, Kestrel ignora quasi sempre il nome host. L'host `localhost` è un caso speciale usato per l'associazione agli indirizzi di loopback. Qualsiasi host che non sia un indirizzo IP esplicito esegue l'associazione a tutti gli indirizzi IP pubblici. Le intestazioni `Host` non vengono convalidate.

Come soluzione alternativa, usare il middleware di filtro host. Il middleware di filtro host viene fornito dal pacchetto [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , fornito in modo implicito per le app ASP.NET Core. Il middleware viene aggiunto da <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, che chiama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering%2A>:

[!code-csharp[](samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Per impostazione predefinita, il middleware di filtro host è disabilitato per impostazione predefinita. Per abilitare il middleware, definire una `AllowedHosts` chiave in *appsettings.json* / *appSettings. \<EnvironmentName> JSON*. Il valore è un elenco con valori delimitati da punto e virgola di nomi host senza numeri di porta:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> Il [middleware delle intestazioni inoltrate](xref:host-and-deploy/proxy-load-balancer) include anche un'opzione <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>. Il middleware di intestazioni inoltrate e il middleware di filtro host hanno funzionalità simili per diversi scenari. L'impostazione di `AllowedHosts` con il middleware delle intestazioni inoltrate è appropriato se l'intestazione `Host` non viene mantenuta durante l'inoltro delle richieste con un server proxy inverso o il bilanciamento del carico. L'impostazione di `AllowedHosts` con il middleware del filtro host è appropriata se si usa Kestrel come server perimetrale rivolto al pubblico o se l'intestazione `Host` viene inoltrata direttamente.
>
> Per altre informazioni sul middleware delle intestazioni inoltrate, vedere <xref:host-and-deploy/proxy-load-balancer>.
