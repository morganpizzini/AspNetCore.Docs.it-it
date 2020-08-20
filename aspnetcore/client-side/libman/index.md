---
title: Acquisizione di una libreria lato client in ASP.NET Core con LibMan
author: scottaddie
description: Informazioni su come installare asset di una libreria lato client in un progetto ASP.NET Core tramite Gestione librerie (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
no-loc:
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
uid: client-side/libman/index
ms.openlocfilehash: 8458f2fe1cdc139a3f22c7d73675301d89ad368e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625635"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>Acquisizione di una libreria lato client in ASP.NET Core con LibMan

Di [Scott Addie](https://twitter.com/Scott_Addie)

Gestione librerie (LibMan) è uno strumento per l'acquisizione di librerie lato client leggere. LibMan scarica librerie e framework popolari dal file system o da una [rete per la distribuzione di contenuti (CDN)](https://wikipedia.org/wiki/Content_delivery_network). I CDNs supportati includono [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)e [unpkg](https://unpkg.com/#/). I file della libreria selezionata vengono recuperati e inseriti nella posizione appropriata all'interno del progetto ASP.NET Core.

## <a name="libman-use-cases"></a>Casi d'uso di LibMan

LibMan offre i vantaggi seguenti:

* Vengono scaricati solo i file di libreria necessari.
* Non sono necessari strumenti aggiuntivi, come [Node.js](https://nodejs.org), [npm](https://www.npmjs.com) e [WebPack](https://webpack.js.org) per acquisire un subset di file in una libreria.
* I file possono essere collocati in una posizione specifica senza dovere usare alcuna attività di compilazione o ricorrere alla copia dei file manuale.

Per altre informazioni sui vantaggi del LibMan, guardare [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s) (Sviluppo di Web front-end moderni con Visual Studio 2017: segmento LibMan).

LibMan non è un sistema di gestione pacchetti. Se si usa già una gestione pacchetti, ad esempio npm oppure [yarn](https://yarnpkg.com), continuare a farlo. LibMan non è stato sviluppato per sostituire questi strumenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [Repository GitHub di LibMan](https://github.com/aspnet/LibraryManager)
