---
title: Gestire gli errori nelle API Web di ASP.NET Core
author: pranavkm
description: Informazioni sulla gestione degli errori con ASP.NET Core le API Web.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 12/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/handle-errors
ms.openlocfilehash: 7c641fb12e0d06ebd7bb3ce9f878f0469b4a3d8e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775063"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="0a9bf-103">Gestire gli errori nelle API Web di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a9bf-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="0a9bf-104">Questo articolo descrive come gestire e personalizzare la gestione degli errori con ASP.NET Core le API Web.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="0a9bf-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0a9bf-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="0a9bf-106">Pagina delle eccezioni per gli sviluppatori</span><span class="sxs-lookup"><span data-stu-id="0a9bf-106">Developer Exception Page</span></span>

<span data-ttu-id="0a9bf-107">La [pagina di eccezione Developer](xref:fundamentals/error-handling) è uno strumento utile per ottenere analisi dettagliate dello stack per gli errori del server.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="0a9bf-108">USA <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> per acquisire eccezioni sincrone e asincrone dalla pipeline HTTP e per generare risposte di errore.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="0a9bf-109">Per illustrare, prendere in considerazione la seguente azione del controller:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="0a9bf-110">Eseguire il comando `curl` seguente per testare l'azione precedente:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0a9bf-111">In ASP.NET Core 3,0 e versioni successive, la pagina delle eccezioni per sviluppatori Visualizza una risposta di testo normale se il client non richiede l'output in formato HTML.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="0a9bf-112">Viene visualizzato l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="0a9bf-113">Per visualizzare invece una risposta in formato HTML, impostare l' `Accept` intestazione della richiesta HTTP sul `text/html` tipo di supporto.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="0a9bf-114">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="0a9bf-115">Si consideri il seguente estratto della risposta HTTP:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="0a9bf-116">In ASP.NET Core 2,2 e versioni precedenti, nella pagina eccezione Developer viene visualizzata una risposta in formato HTML.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="0a9bf-117">Si consideri, ad esempio, il seguente estratto della risposta HTTP:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0a9bf-118">La risposta in formato HTML risulta utile quando si esegue il test tramite strumenti come il post.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="0a9bf-119">L'acquisizione di schermate seguente mostra sia il testo normale che le risposte in formato HTML in post:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Test della pagina di eccezione per gli sviluppatori nel post](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="0a9bf-121">Abilitare la pagina delle eccezioni **per gli sviluppatori solo quando l'app è in esecuzione nell'ambiente di sviluppo**.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="0a9bf-122">per non condividere pubblicamente le informazioni dettagliate sulle eccezioni quando l'app è in esecuzione nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-122">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="0a9bf-123">Per altre informazioni sulla configurazione di ambienti, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="0a9bf-124">Gestore di eccezioni</span><span class="sxs-lookup"><span data-stu-id="0a9bf-124">Exception handler</span></span>

<span data-ttu-id="0a9bf-125">Negli ambienti non di sviluppo, è possibile usare il [middleware di gestione delle eccezioni](xref:fundamentals/error-handling) per produrre un payload degli errori:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-125">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="0a9bf-126">In `Startup.Configure`richiamare <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> per usare il middleware:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-126">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="0a9bf-127">Configurare un'azione del `/error` controller per rispondere alla route:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-127">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="0a9bf-128">L'azione `Error` precedente invia al client un payload conforme a [RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="0a9bf-128">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="0a9bf-129">Il middleware di gestione delle eccezioni può inoltre fornire un output più dettagliato del contenuto negoziato nell'ambiente di sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-129">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="0a9bf-130">Usare la procedura seguente per produrre un formato di payload coerente negli ambienti di sviluppo e di produzione:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-130">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="0a9bf-131">In `Startup.Configure`registrare le istanze del middleware di gestione delle eccezioni specifiche dell'ambiente:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-131">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="0a9bf-132">Nel codice precedente il middleware è registrato con:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-132">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="0a9bf-133">Route di nell' `/error-local-development` ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-133">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="0a9bf-134">Una route di `/error` in ambienti che non sono in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-134">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="0a9bf-135">Applicare il routing degli attributi alle azioni del controller:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-135">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="0a9bf-136">Usare le eccezioni per modificare la risposta</span><span class="sxs-lookup"><span data-stu-id="0a9bf-136">Use exceptions to modify the response</span></span>

<span data-ttu-id="0a9bf-137">Il contenuto della risposta può essere modificato dall'esterno del controller.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-137">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="0a9bf-138">Nell'API Web ASP.NET 4. x, un modo per eseguire questa operazione consiste nell' <xref:System.Web.Http.HttpResponseException> usare il tipo.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-138">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="0a9bf-139">ASP.NET Core non include un tipo equivalente.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-139">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="0a9bf-140">È possibile `HttpResponseException` aggiungere il supporto per con i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-140">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="0a9bf-141">Creare un tipo di eccezione noto denominato `HttpResponseException`:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-141">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="0a9bf-142">Creare un filtro azione denominato `HttpResponseExceptionFilter`:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-142">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. <span data-ttu-id="0a9bf-143">In `Startup.ConfigureServices`aggiungere il filtro azioni alla raccolta filters:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-143">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="0a9bf-144">Risposta errore di convalida non riuscita</span><span class="sxs-lookup"><span data-stu-id="0a9bf-144">Validation failure error response</span></span>

<span data-ttu-id="0a9bf-145">Per i controller API Web, MVC risponde con un <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> tipo di risposta quando la convalida del modello ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-145">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="0a9bf-146">MVC usa i risultati di <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> per costruire la risposta di errore per un errore di convalida.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-146">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="0a9bf-147">Nell'esempio seguente viene usata la factory per modificare il tipo di risposta <xref:Microsoft.AspNetCore.Mvc.SerializableError> predefinito `Startup.ConfigureServices`in:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-147">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="0a9bf-148">Risposta errore client</span><span class="sxs-lookup"><span data-stu-id="0a9bf-148">Client error response</span></span>

<span data-ttu-id="0a9bf-149">Un *risultato di errore* viene definito come risultato con un codice di stato HTTP 400 o superiore.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-149">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="0a9bf-150">Per i controller API Web, MVC trasforma un risultato di errore in un risultato con <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-150">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="0a9bf-151">ASP.NET Core 2,1 genera una risposta dettagliata al problema che è quasi conforme a RFC 7807.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-151">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="0a9bf-152">Se è importante la conformità al 100%, aggiornare il progetto a ASP.NET Core 2,2 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-152">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0a9bf-153">La risposta di errore può essere configurata in uno dei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-153">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="0a9bf-154">Implementare ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="0a9bf-154">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="0a9bf-155">Usare ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="0a9bf-155">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="0a9bf-156">Implementare ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="0a9bf-156">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="0a9bf-157">MVC utilizza `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` per produrre tutte le istanze <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> di <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>e.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-157">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="0a9bf-158">Sono incluse le risposte degli errori del client, le risposte degli errori `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` di <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> convalida e i metodi helper e.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-158">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="0a9bf-159">Per personalizzare la risposta dettagliata al problema, registrare un'implementazione personalizzata `ProblemDetailsFactory` di `Startup.ConfigureServices`in:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-159">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="0a9bf-160">La risposta di errore può essere configurata come descritto nella sezione [use ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) .</span><span class="sxs-lookup"><span data-stu-id="0a9bf-160">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="0a9bf-161">Usare ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="0a9bf-161">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="0a9bf-162">Usare la proprietà <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> per configurare il contenuto della risposta `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="0a9bf-162">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="0a9bf-163">Ad esempio, il codice seguente in `Startup.ConfigureServices` aggiorna la `type` proprietà per le risposte 404:</span><span class="sxs-lookup"><span data-stu-id="0a9bf-163">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
