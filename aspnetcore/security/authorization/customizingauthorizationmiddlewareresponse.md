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
# <a name="customize-the-behavior-of-authorizationmiddleware"></a>Personalizzare il comportamento di AuthorizationMiddleware

Le applicazioni possono registrare un `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` per personalizzare il modo in cui il middleware gestisce i risultati dell'autorizzazione. Le applicazioni possono usare il middleware personalizzato per:

* Restituisce risposte personalizzate.
* Migliorare la richiesta o la proibizione delle risposte predefinite.

Nel codice seguente viene illustrato un esempio di un gestore di autorizzazione che restituisce una risposta personalizzata per determinati tipi di errori di autorizzazione:

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

Registra `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices` :

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->