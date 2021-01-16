---
title: Quando usare un proxy inverso con il server Web ASP.NET Core gheppio
author: rick-anderson
description: Informazioni su quando usare un proxy inverso davanti a gheppio, il server Web multipiattaforma per ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253940"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Quando usare Kestrel con un proxy inverso

È possibile usare Kestrel da solo o in combinazione con un *server proxy inverso*, ad esempio [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/). Il server proxy inverso riceve le richieste HTTP dalla rete e le inoltra a Kestrel.

Kestrel usato come server Web perimetrale (esposto a Internet):

![Kestrel comunica direttamente con Internet senza un server proxy inverso](_static/kestrel-to-internet2.png)

Kestrel usato in una configurazione proxy inverso:

![Kestrel comunica indirettamente con Internet attraverso un server proxy inverso, ad esempio IIS, Nginx o Apache](_static/kestrel-to-internet.png)

La configurazione, con o senza un server proxy inverso, è una configurazione di hosting supportata.

Quando il gheppio viene usato come server perimetrale senza un server proxy inverso, la condivisione dello stesso indirizzo IP e della stessa porta tra più processi non è supportata. Quando il gheppio è configurato per l'ascolto su una porta, gheppio gestisce tutto il traffico per tale porta indipendentemente dalle intestazioni delle richieste `Host` . Un proxy inverso che può condividere le porte può inviare le richieste a gheppio su un IP e una porta univoci.

Anche se la presenza di un server proxy inverso non è necessaria, può risultare utile per i motivi seguenti.

Un proxy inverso:

* Può limitare l'area della superficie di attacco pubblica esposta delle app ospitate.
* Offre un livello aggiuntivo di configurazione e protezione.
* Potrebbe offrire un'integrazione migliore con l'infrastruttura esistente.
* Semplifica il bilanciamento del carico e la configurazione di comunicazioni protette (HTTPS). Solo il server proxy inverso richiede un certificato X. 509 e il server è in grado di comunicare con i server dell'app nella rete interna tramite HTTP normale.

> [!WARNING]
> Una configurazione che prevede un proxy inverso richiede il [filtro host](xref:fundamentals/servers/kestrel/host-filtering).

## <a name="additional-resources"></a>Risorse aggiuntive

<xref:host-and-deploy/proxy-load-balancer>

