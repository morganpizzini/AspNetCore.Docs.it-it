---
title: Scrivere middleware di ASP.NET Core personalizzato
author: rick-anderson
description: Informazioni su come scrivere middleware di ASP.NET Core personalizzato.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/write
ms.openlocfilehash: 6852c1831d1f71af7f4fad3288fd6f897dfaa65f
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876192"
---
# <a name="write-custom-aspnet-core-middleware"></a>Scrivere middleware di ASP.NET Core personalizzato

Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)

Il middleware è un software che viene assemblato in una pipeline dell'app per gestire richieste e risposte. ASP.NET Core offre un ampio set di componenti middleware integrati, ma in alcuni scenari si potrebbe voler scrivere un middleware personalizzato.

## <a name="middleware-class"></a>Classe middleware

Il middleware è in genere incapsulato in una classe ed esposto con un metodo di estensione. Si consideri il middleware seguente, che specifica le impostazioni cultura per la richiesta corrente da una stringa di query:

[!code-csharp[](write/snapshot/StartupCulture.cs)]

Il codice di esempio precedente viene usato per illustrare la creazione di un componente middleware. Per il supporto di localizzazione incorporato di ASP.NET Core, vedere <xref:fundamentals/localization>.

Testare il middleware passando le impostazioni cultura. Ad esempio, richiedere `https://localhost:5001/?culture=no`.

Il codice seguente sposta il delegato middleware in una classe:

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

La classe middleware deve includere:

* Un costruttore pubblico con un parametro di tipo <xref:Microsoft.AspNetCore.Http.RequestDelegate>.
* Un metodo pubblico denominato `Invoke` o `InvokeAsync`. Questo metodo deve:
  * Restituire `Task`.
  * Accettare un primo parametro di tipo <xref:Microsoft.AspNetCore.Http.HttpContext>.
  
I parametri aggiuntivi per il costruttore e `Invoke`/`InvokeAsync` vengono popolati dall'[inserimento delle dipendenze](xref:fundamentals/dependency-injection).

## <a name="middleware-dependencies"></a>Dipendenze del middleware

Il middleware deve seguire il [principio delle dipendenze esplicite](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) esponendo le dipendenze nel costruttore. Il middleware viene costruito una volta per ogni *durata applicazione*. Se è necessario condividere servizi con il middleware all'interno di una richiesta, vedere la sezione [Dipendenze del middleware per richiesta](#per-request-middleware-dependencies).

I componenti middleware possono risolvere le dipendenze dall'[inserimento di dipendenze](xref:fundamentals/dependency-injection) mediante i parametri del costruttore. [UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) può anche accettare direttamente parametri aggiuntivi.

## <a name="per-request-middleware-dependencies"></a>Dipendenze del middleware per richiesta

Poiché il middleware viene creato all'avvio dell'app e non per richiesta, i servizi di durata *con ambito* usati dai costruttori del middleware non vengono condivisi con altri tipi di inserimento di dipendenze durante ogni richiesta. Se è necessario condividere un servizio *con ambito* tra il proprio middleware e altri tipi, aggiungere i servizi alla firma del metodo `Invoke`. Il metodo `Invoke` può accettare parametri aggiuntivi popolati dall'inserimento delle dipendenze:

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a>Metodo di estensione del middleware

Il metodo di estensione seguente espone il middleware tramite <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

Il codice seguente chiama il middleware da `Startup.Configure`:

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
