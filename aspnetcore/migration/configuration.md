---
title: Eseguire la migrazione della configurazione a ASP.NET Core
author: ardalis
description: Informazioni su come eseguire la migrazione della configurazione da un progetto MVC ASP.NET a un progetto MVC ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/configuration
ms.openlocfilehash: f65db927d79224695861101aff00897315c6e0b2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777228"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="cdbe8-103">Eseguire la migrazione della configurazione a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cdbe8-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="cdbe8-104">Di [Steve Smith](https://ardalis.com/) e [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="cdbe8-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="cdbe8-105">Nell'articolo precedente è stata avviata la [migrazione di un progetto mvc ASP.NET a ASP.NET Core MVC](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="cdbe8-105">In the previous article, we began to [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="cdbe8-106">In questo articolo viene eseguita la migrazione della configurazione.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="cdbe8-107">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cdbe8-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="cdbe8-108">Eseguire la configurazione</span><span class="sxs-lookup"><span data-stu-id="cdbe8-108">Setup configuration</span></span>

<span data-ttu-id="cdbe8-109">ASP.NET Core non utilizza più i file *Global. asax* e *Web. config* utilizzati dalle versioni precedenti di ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="cdbe8-110">Nelle versioni precedenti di ASP.NET, la logica di avvio dell'applicazione è stata `Application_StartUp` inserita in un metodo all'interno di *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-110">In the earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="cdbe8-111">In seguito, in ASP.NET MVC, è stato incluso un file *Startup.cs* nella radice del progetto. e è stato chiamato all'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="cdbe8-112">ASP.NET Core ha adottato completamente questo approccio inserendo tutta la logica di avvio nel file *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="cdbe8-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="cdbe8-113">Il file *Web. config* è stato sostituito anche in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="cdbe8-114">È ora possibile configurare la configurazione, come parte della procedura di avvio dell'applicazione descritta in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="cdbe8-115">La configurazione può comunque utilizzare file XML, ma in genere ASP.NET Core progetti inseriranno i valori di configurazione in un file in formato JSON, ad esempio *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="cdbe8-116">Il sistema di configurazione di ASP.NET Core può anche accedere facilmente alle variabili di ambiente, che possono fornire una [posizione più sicura e affidabile](xref:security/app-secrets) per i valori specifici dell'ambiente.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="cdbe8-117">Questa operazione è particolarmente valida per i segreti come le stringhe di connessione e le chiavi API che non devono essere archiviate nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="cdbe8-118">Per ulteriori informazioni sulla configurazione, vedere la pagina relativa alla [configurazione](xref:fundamentals/configuration/index) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="cdbe8-119">Per questo articolo, si inizia con il progetto di ASP.NET Core parzialmente migrato dall' [articolo precedente](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="cdbe8-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="cdbe8-120">Per configurare la configurazione, aggiungere il costruttore e la proprietà seguenti al file *Startup.cs* che si trova nella radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="cdbe8-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="cdbe8-121">Si noti che a questo punto il file *Startup.cs* non verrà compilato, perché è ancora necessario aggiungere l'istruzione `using` seguente:</span><span class="sxs-lookup"><span data-stu-id="cdbe8-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="cdbe8-122">Aggiungere un file *appSettings. JSON* alla radice del progetto usando il modello di elemento appropriato:</span><span class="sxs-lookup"><span data-stu-id="cdbe8-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![Aggiungi codice JSON AppSettings](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="cdbe8-124">Eseguire la migrazione delle impostazioni di configurazione da Web. config</span><span class="sxs-lookup"><span data-stu-id="cdbe8-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="cdbe8-125">Il progetto ASP.NET MVC includeva la stringa di connessione del database necessaria in *Web. config*, nell' `<connectionStrings>` elemento.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="cdbe8-126">Nel progetto ASP.NET Core le informazioni verranno archiviate nel file *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="cdbe8-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="cdbe8-127">Aprire *appSettings. JSON*e tenere presente che include già quanto segue:</span><span class="sxs-lookup"><span data-stu-id="cdbe8-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="cdbe8-128">Nella riga evidenziata sopra, modificare il nome del database da **_CHANGE_ME** al nome del database.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="cdbe8-129">Summary</span><span class="sxs-lookup"><span data-stu-id="cdbe8-129">Summary</span></span>

<span data-ttu-id="cdbe8-130">ASP.NET Core inserisce tutta la logica di avvio per l'applicazione in un unico file, in cui è possibile definire e configurare le dipendenze e i servizi necessari.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="cdbe8-131">Sostituisce il file *Web. config* con una funzionalità di configurazione flessibile che può utilizzare una varietà di formati di file, ad esempio JSON, e variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="cdbe8-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
