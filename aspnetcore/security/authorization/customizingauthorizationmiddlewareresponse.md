---
title: Personalizzare il comportamento di AuthorizationMiddleware
author: pranavkm
ms.author: prkrishn
description: Questo articolo illustra come personalizzare la gestione dei risultati di AuthorizationMiddleware.
monikerRange: '>= aspnetcore-5.0'
uid: security/authorization/authorizationmiddlewareresulthandler
ms.openlocfilehash: 55f4433c080d6ce6676ca1072dacacc137f15638
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92156784"
---
# <a name="customize-the-behavior-of-authorizationmiddleware"></a><span data-ttu-id="9f6f8-103">Personalizzare il comportamento di AuthorizationMiddleware</span><span class="sxs-lookup"><span data-stu-id="9f6f8-103">Customize the behavior of AuthorizationMiddleware</span></span>

<span data-ttu-id="9f6f8-104">Le applicazioni possono registrare un `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` per personalizzare il modo in cui il middleware gestisce i risultati dell'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="9f6f8-104">Applications can register a `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` to customize the way the middleware handles the authorization results.</span></span> <span data-ttu-id="9f6f8-105">Le applicazioni possono usare il middleware personalizzato per:</span><span class="sxs-lookup"><span data-stu-id="9f6f8-105">Applications can use the customized middleware to:</span></span>

* <span data-ttu-id="9f6f8-106">Restituisce risposte personalizzate.</span><span class="sxs-lookup"><span data-stu-id="9f6f8-106">Return customized responses.</span></span>
* <span data-ttu-id="9f6f8-107">Migliorare la richiesta o la proibizione delle risposte predefinite.</span><span class="sxs-lookup"><span data-stu-id="9f6f8-107">Enhance the default challenge or forbid responses.</span></span>

<span data-ttu-id="9f6f8-108">Nel codice seguente viene illustrato un esempio di un gestore di autorizzazione che restituisce una risposta personalizzata per determinati tipi di errori di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="9f6f8-108">The following code shows an example of an authorization handler that returns a custom response for certain kinds of authorization failures:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

<span data-ttu-id="9f6f8-109">Registra `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9f6f8-109">Register `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->