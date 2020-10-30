---
title: "Esercitazione: creare un' :::no-loc(Razor)::: app Web di pagine con ASP.NET Core"
author: rick-anderson
description: "Creare un' :::no-loc(Razor)::: app Web di pagine in Windows con Visual Studio, ASP.NET Core e EF core."
ms.author: riande
ms.date: 08/09/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/razor-pages/index
ms.openlocfilehash: 0feb60c7e0189bf12d584c3916e0254b3be729c0
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058064"
---
# <a name="tutorial-create-a-no-locrazor-pages-web-app-with-aspnet-core"></a><span data-ttu-id="115e0-103">Esercitazione: creare un' :::no-loc(Razor)::: app Web di pagine con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="115e0-103">Tutorial: Create a :::no-loc(Razor)::: Pages web app with ASP.NET Core</span></span>

<span data-ttu-id="115e0-104">Questa serie di esercitazioni illustra le nozioni di base della creazione di un' :::no-loc(Razor)::: app Web di pagine.</span><span class="sxs-lookup"><span data-stu-id="115e0-104">This series of tutorials explains the basics of building a :::no-loc(Razor)::: Pages web app.</span></span> 

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="115e0-105">La serie include le esercitazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="115e0-105">This series includes the following tutorials:</span></span>

1. [<span data-ttu-id="115e0-106">Creare un' :::no-loc(Razor)::: app Web di pagine</span><span class="sxs-lookup"><span data-stu-id="115e0-106">Create a :::no-loc(Razor)::: Pages web app</span></span>](xref:tutorials/razor-pages/razor-pages-start)
1. [<span data-ttu-id="115e0-107">Aggiungere un modello a un' :::no-loc(Razor)::: app pagine</span><span class="sxs-lookup"><span data-stu-id="115e0-107">Add a model to a :::no-loc(Razor)::: Pages app</span></span>](xref:tutorials/razor-pages/model)
1. [<span data-ttu-id="115e0-108">Pagine di impalcature (generate) :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="115e0-108">Scaffold (generate) :::no-loc(Razor)::: pages</span></span>](xref:tutorials/razor-pages/page)
1. [<span data-ttu-id="115e0-109">Usare un database</span><span class="sxs-lookup"><span data-stu-id="115e0-109">Work with a database</span></span>](xref:tutorials/razor-pages/sql)
1. [<span data-ttu-id="115e0-110">Aggiorna :::no-loc(Razor)::: pagine</span><span class="sxs-lookup"><span data-stu-id="115e0-110">Update :::no-loc(Razor)::: pages</span></span>](xref:tutorials/razor-pages/da1)
1. [<span data-ttu-id="115e0-111">Aggiungere la funzionalità di ricerca</span><span class="sxs-lookup"><span data-stu-id="115e0-111">Add search</span></span>](xref:tutorials/razor-pages/search)
1. [<span data-ttu-id="115e0-112">Aggiungere un nuovo campo</span><span class="sxs-lookup"><span data-stu-id="115e0-112">Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
1. [<span data-ttu-id="115e0-113">Aggiungere la convalida</span><span class="sxs-lookup"><span data-stu-id="115e0-113">Add validation</span></span>](xref:tutorials/razor-pages/validation)

<span data-ttu-id="115e0-114">Al termine, si otterrà un'app che consente di visualizzare e gestire un database di film.</span><span class="sxs-lookup"><span data-stu-id="115e0-114">At the end, you'll have an app that can display and manage a database of movies.</span></span>

![Pagina di esempio dell'app di esempio](index/_static/sample-page.png)
