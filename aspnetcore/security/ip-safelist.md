---
title: Client IP safelist for ASP.NET Core
author: damienbod
description: Informazioni su come scrivere filtri middleware o action per convalidare gli indirizzi IP remoti in base a un elenco di indirizzi IP approvati.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471784"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>Client IP safelist for ASP.NET Core

Di [Damien Bowden](https://twitter.com/damien_bod) e [Tom Dykstra](https://github.com/tdykstra)
 
Questo articolo illustra tre modi per implementare un elenco indirizzi IP attendibili (noto anche come elenco Consenti) in un'app ASP.NET Core.This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app. Un'app di esempio di accompagnamento illustra tutti e tre gli approcci. È possibile usare:

* Middleware per controllare l'indirizzo IP remoto di ogni richiesta.
* Filtri di azione MVC per controllare l'indirizzo IP remoto delle richieste per controller o metodi di azione specifici.
* Filtri Razor Pages per controllare l'indirizzo IP remoto delle richieste per le pagine Razor.

In ogni caso, una stringa contenente gli indirizzi IP dei client approvati viene archiviata in un'impostazione dell'app. Il middleware o il filtro:

* Analizza la stringa in una matrice. 
* Controlla se l'indirizzo IP remoto esiste nell'array.

L'accesso è consentito se l'array contiene l'indirizzo IP. In caso contrario, viene restituito un codice di stato HTTP 403 Forbidden.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>Elenco indirizzi IP attendibili

Nell'app di esempio, l'elenco indirizzi IP attendibili è:In the sample app, the IP address safelist is:

* Definito dalla `AdminSafeList` proprietà nel file *appsettings.json.*
* Stringa delimitata da punti e virgola che può contenere indirizzi [IPv4 (Internet Protocol versione 4)](https://wikipedia.org/wiki/IPv4) e [IPv6 (Internet Protocol versione 6).](https://wikipedia.org/wiki/IPv6)

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

Nell'esempio precedente sono consentiti gli `127.0.0.1` `192.168.1.5` indirizzi IPv4 di e `::1` e l'indirizzo di loopback IPv6 di (formato compresso per `0:0:0:0:0:0:0:1`).

## <a name="middleware"></a>Middleware

Il `Startup.Configure` metodo aggiunge `AdminSafeListMiddleware` il tipo di middleware personalizzato alla pipeline delle richieste dell'app. L'elenco indirizzi attendibili viene recuperato con il provider di configurazione .NET Core e viene passato come parametro del costruttore.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

Il middleware analizza la stringa in una matrice e cerca l'indirizzo IP remoto nella matrice. Se l'indirizzo IP remoto non viene trovato, il middleware restituisce HTTP 403 non consentito. Questo processo di convalida viene ignorato per le richieste HTTP GET.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Filtro azione

Se si desidera un controllo di accesso basato su elenchi indirizzi attendibili per controller MVC o metodi di azione specifici, utilizzare un filtro azione. Ad esempio:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

In `Startup.ConfigureServices`, aggiungere il filtro azione all'insieme di filtri MVC. Nell'esempio seguente `ClientIpCheckActionFilter` viene aggiunto un filtro azione. Un elenco indirizzi attendibili e un'istanza del logger di console vengono passati come parametri del costruttore.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

Il filtro azione può quindi essere applicato a un controller o a un metodo di azione con l'attributo [[ServiceFilter]:The](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) action filter can then be applied to a controller or action method with the [ServiceFilter] attribute:

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

Nell'app di esempio, il filtro azione `Get` viene applicato al metodo di azione del controller. Quando si testa l'app inviando:

* Una richiesta HTTP `[ServiceFilter]` GET, l'attributo convalida l'indirizzo IP del client. Se l'accesso `Get` al metodo di azione è consentito, una variazione dell'output della console seguente viene prodotta dal filtro azione e dal metodo di azione:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* Un verbo di richiesta `AdminSafeListMiddleware` HTTP diverso da GET, il middleware convalida l'indirizzo IP del client.

## <a name="razor-pages-filter"></a>Filtro Pagine Razor

Se vuoi il controllo dell'accesso basato su elenchi indirizzi attendibili per un'app Razor Pages, usa un filtro Razor Pages. Ad esempio:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

In `Startup.ConfigureServices`, abilitare il filtro Pagine Razor aggiungendolo all'insieme di filtri MVC. Nell'esempio seguente `ClientIpCheckPageFilter` viene aggiunto un filtro Razor Pages.In the following example, a Razor Pages filter is added. Un elenco indirizzi attendibili e un'istanza del logger di console vengono passati come parametri del costruttore.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Quando viene richiesta la pagina *Razor indice* dell'app di esempio, il filtro Razor Pages convalida l'indirizzo IP del client. Il filtro produce una variazione del seguente output della console:

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/middleware/index>
* [Filtri azione](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
