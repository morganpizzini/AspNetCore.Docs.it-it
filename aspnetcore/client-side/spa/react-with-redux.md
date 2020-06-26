---
title: Usare il modello di progetto per React con Redux con ASP.NET Core
author: SteveSandersonMS
description: Informazioni su come iniziare a usare il modello di progetto per applicazioni a pagina singola di ASP.NET Core per React con Redux e create-react-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: spa/react-with-redux
ms.openlocfilehash: d44e1afabd909b7d46d3355a5d66848458416873
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401740"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="6bb5a-103">Usare il modello di progetto per React con Redux con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6bb5a-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="6bb5a-104">Il modello di progetto aggiornato per React con Redux fornisce un ottimo punto di partenza per le app ASP.NET Core che usano le convenzioni React, Redux e [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) per implementare un'interfaccia utente avanzata sul lato client.</span><span class="sxs-lookup"><span data-stu-id="6bb5a-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="6bb5a-105">Fatta eccezione per il comando di creazione del progetto, tutte le informazioni relative al modello per React con Redux sono uguali a quelle relative al modello per React.</span><span class="sxs-lookup"><span data-stu-id="6bb5a-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="6bb5a-106">Per creare questo tipo di progetto, eseguire `dotnet new reactredux` anziché `dotnet new react`.</span><span class="sxs-lookup"><span data-stu-id="6bb5a-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="6bb5a-107">Per altre informazioni sulle funzionalità comuni a entrambi i modelli basati su React, vedere la [documentazione del modello per React](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="6bb5a-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="6bb5a-108">Per informazioni sulla configurazione di un'applicazione secondaria React-with-Redux in IIS, vedere il [modello ReactRedux 2,1: Impossibile utilizzare Spa in IIS (ASPNET/template &num; 555)](https://github.com/aspnet/Templating/issues/555).</span><span class="sxs-lookup"><span data-stu-id="6bb5a-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
