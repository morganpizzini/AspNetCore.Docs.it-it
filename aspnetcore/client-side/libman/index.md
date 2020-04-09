---
title: Acquisizione di una libreria lato client in ASP.NET Core con LibMan
author: scottaddie
description: Informazioni su come installare asset di una libreria lato client in un progetto ASP.NET Core tramite Gestione librerie (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: 87987446b7f2c625da90951510e697e06569ba36
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664668"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a><span data-ttu-id="b4b19-103">Acquisizione di una libreria lato client in ASP.NET Core con LibMan</span><span class="sxs-lookup"><span data-stu-id="b4b19-103">Client-side library acquisition in ASP.NET Core with LibMan</span></span>

<span data-ttu-id="b4b19-104">Di [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="b4b19-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="b4b19-105">Gestione librerie (LibMan) è uno strumento per l'acquisizione di librerie lato client leggere.</span><span class="sxs-lookup"><span data-stu-id="b4b19-105">Library Manager (LibMan) is a lightweight, client-side library acquisition tool.</span></span> <span data-ttu-id="b4b19-106">LibMan scarica librerie e framework popolari dal file system o da una [rete per la distribuzione di contenuti (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span><span class="sxs-lookup"><span data-stu-id="b4b19-106">LibMan downloads popular libraries and frameworks from the file system or from a [content delivery network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span></span> <span data-ttu-id="b4b19-107">Le reti CDN supportate includono [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)e [unpkg](https://unpkg.com/#/).</span><span class="sxs-lookup"><span data-stu-id="b4b19-107">The supported CDNs include [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/), and [unpkg](https://unpkg.com/#/).</span></span> <span data-ttu-id="b4b19-108">I file della libreria selezionata vengono recuperati e inseriti nella posizione appropriata all'interno del progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b4b19-108">The selected library files are fetched and placed in the appropriate location within the ASP.NET Core project.</span></span>

## <a name="libman-use-cases"></a><span data-ttu-id="b4b19-109">Casi d'uso di LibMan</span><span class="sxs-lookup"><span data-stu-id="b4b19-109">LibMan use cases</span></span>

<span data-ttu-id="b4b19-110">LibMan offre i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="b4b19-110">LibMan offers the following benefits:</span></span>

* <span data-ttu-id="b4b19-111">Vengono scaricati solo i file di libreria necessari.</span><span class="sxs-lookup"><span data-stu-id="b4b19-111">Only the library files you need are downloaded.</span></span>
* <span data-ttu-id="b4b19-112">Non sono necessari strumenti aggiuntivi, come [Node.js](https://nodejs.org), [npm](https://www.npmjs.com) e [WebPack](https://webpack.js.org) per acquisire un subset di file in una libreria.</span><span class="sxs-lookup"><span data-stu-id="b4b19-112">Additional tooling, such as [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), and [WebPack](https://webpack.js.org), isn't necessary to acquire a subset of files in a library.</span></span>
* <span data-ttu-id="b4b19-113">I file possono essere collocati in una posizione specifica senza dovere usare alcuna attività di compilazione o ricorrere alla copia dei file manuale.</span><span class="sxs-lookup"><span data-stu-id="b4b19-113">Files can be placed in a specific location without resorting to build tasks or manual file copying.</span></span>

<span data-ttu-id="b4b19-114">Per altre informazioni sui vantaggi del LibMan, guardare [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s) (Sviluppo di Web front-end moderni con Visual Studio 2017: segmento LibMan).</span><span class="sxs-lookup"><span data-stu-id="b4b19-114">For more information about LibMan's benefits, watch [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span></span>

<span data-ttu-id="b4b19-115">LibMan non è un sistema di gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="b4b19-115">LibMan isn't a package management system.</span></span> <span data-ttu-id="b4b19-116">Se si usa già una gestione pacchetti, ad esempio npm oppure [yarn](https://yarnpkg.com), continuare a farlo.</span><span class="sxs-lookup"><span data-stu-id="b4b19-116">If you're already using a package manager, such as npm or [yarn](https://yarnpkg.com), continue doing so.</span></span> <span data-ttu-id="b4b19-117">LibMan non è stato sviluppato per sostituire questi strumenti.</span><span class="sxs-lookup"><span data-stu-id="b4b19-117">LibMan wasn't developed to replace those tools.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4b19-118">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b4b19-118">Additional resources</span></span>

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="b4b19-119">Repository GitHub di LibMan</span><span class="sxs-lookup"><span data-stu-id="b4b19-119">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
