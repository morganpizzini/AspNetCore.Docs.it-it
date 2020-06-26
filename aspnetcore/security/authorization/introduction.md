---
title: Introduzione all'autorizzazione in ASP.NET Core
author: rick-anderson
description: Informazioni sulle nozioni di base sull'autorizzazione e sul funzionamento dell'autorizzazione nelle app ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/introduction
ms.openlocfilehash: b0d6c91adfb5dc273aeb662cb2c249626ef1d3c6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405432"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="7a6ca-103">Introduzione all'autorizzazione in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a6ca-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="7a6ca-104">L'autorizzazione si riferisce al processo che determina ciò che un utente è in grado di eseguire.</span><span class="sxs-lookup"><span data-stu-id="7a6ca-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="7a6ca-105">Ad esempio, un utente amministratore può creare una raccolta documenti, aggiungere documenti, modificare documenti ed eliminarli.</span><span class="sxs-lookup"><span data-stu-id="7a6ca-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="7a6ca-106">Un utente non amministratore che utilizza la libreria è autorizzato solo a leggere i documenti.</span><span class="sxs-lookup"><span data-stu-id="7a6ca-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="7a6ca-107">L'autorizzazione è ortogonale e indipendente dall'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="7a6ca-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="7a6ca-108">Tuttavia, l'autorizzazione richiede un meccanismo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="7a6ca-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="7a6ca-109">L'autenticazione è il processo che consente di verificare l'identità di un utente.</span><span class="sxs-lookup"><span data-stu-id="7a6ca-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="7a6ca-110">L'autenticazione, inoltre, può creare una o più identità per l'utente corrente.</span><span class="sxs-lookup"><span data-stu-id="7a6ca-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="7a6ca-111">Per ulteriori informazioni sull'autenticazione in ASP.NET Core, vedere <xref:security/authentication/index> .</span><span class="sxs-lookup"><span data-stu-id="7a6ca-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="7a6ca-112">Tipi di autorizzazione</span><span class="sxs-lookup"><span data-stu-id="7a6ca-112">Authorization types</span></span>

<span data-ttu-id="7a6ca-113">ASP.NET Core autorizzazione fornisce un [ruolo](xref:security/authorization/roles) semplice e dichiarativo e un modello avanzato [basato sui criteri](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="7a6ca-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="7a6ca-114">L'autorizzazione è espressa nei requisiti e i gestori valutano le attestazioni di un utente in base ai requisiti.</span><span class="sxs-lookup"><span data-stu-id="7a6ca-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="7a6ca-115">I controlli imperativi possono essere basati su criteri semplici o criteri che valutano l'identità dell'utente e le proprietà della risorsa a cui l'utente sta tentando di accedere.</span><span class="sxs-lookup"><span data-stu-id="7a6ca-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="7a6ca-116">Spazi dei nomi</span><span class="sxs-lookup"><span data-stu-id="7a6ca-116">Namespaces</span></span>

<span data-ttu-id="7a6ca-117">I componenti di autorizzazione, inclusi gli `AuthorizeAttribute` `AllowAnonymousAttribute` attributi e, si trovano nello `Microsoft.AspNetCore.Authorization` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="7a6ca-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="7a6ca-118">Consultare la documentazione relativa all' [autorizzazione semplice](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="7a6ca-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
