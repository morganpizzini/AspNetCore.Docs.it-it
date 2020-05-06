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
# <a name="migrate-configuration-to-aspnet-core"></a>Eseguire la migrazione della configurazione a ASP.NET Core

Di [Steve Smith](https://ardalis.com/) e [Scott Addie](https://scottaddie.com)

Nell'articolo precedente è stata avviata la [migrazione di un progetto mvc ASP.NET a ASP.NET Core MVC](xref:migration/mvc). In questo articolo viene eseguita la migrazione della configurazione.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Eseguire la configurazione

ASP.NET Core non utilizza più i file *Global. asax* e *Web. config* utilizzati dalle versioni precedenti di ASP.NET. Nelle versioni precedenti di ASP.NET, la logica di avvio dell'applicazione è stata `Application_StartUp` inserita in un metodo all'interno di *Global. asax*. In seguito, in ASP.NET MVC, è stato incluso un file *Startup.cs* nella radice del progetto. e è stato chiamato all'avvio dell'applicazione. ASP.NET Core ha adottato completamente questo approccio inserendo tutta la logica di avvio nel file *Startup.cs* .

Il file *Web. config* è stato sostituito anche in ASP.NET Core. È ora possibile configurare la configurazione, come parte della procedura di avvio dell'applicazione descritta in *Startup.cs*. La configurazione può comunque utilizzare file XML, ma in genere ASP.NET Core progetti inseriranno i valori di configurazione in un file in formato JSON, ad esempio *appSettings. JSON*. Il sistema di configurazione di ASP.NET Core può anche accedere facilmente alle variabili di ambiente, che possono fornire una [posizione più sicura e affidabile](xref:security/app-secrets) per i valori specifici dell'ambiente. Questa operazione è particolarmente valida per i segreti come le stringhe di connessione e le chiavi API che non devono essere archiviate nel controllo del codice sorgente. Per ulteriori informazioni sulla configurazione, vedere la pagina relativa alla [configurazione](xref:fundamentals/configuration/index) ASP.NET Core.

Per questo articolo, si inizia con il progetto di ASP.NET Core parzialmente migrato dall' [articolo precedente](xref:migration/mvc). Per configurare la configurazione, aggiungere il costruttore e la proprietà seguenti al file *Startup.cs* che si trova nella radice del progetto:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Si noti che a questo punto il file *Startup.cs* non verrà compilato, perché è ancora necessario aggiungere l'istruzione `using` seguente:

```csharp
using Microsoft.Extensions.Configuration;
```

Aggiungere un file *appSettings. JSON* alla radice del progetto usando il modello di elemento appropriato:

![Aggiungi codice JSON AppSettings](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Eseguire la migrazione delle impostazioni di configurazione da Web. config

Il progetto ASP.NET MVC includeva la stringa di connessione del database necessaria in *Web. config*, nell' `<connectionStrings>` elemento. Nel progetto ASP.NET Core le informazioni verranno archiviate nel file *appSettings. JSON* . Aprire *appSettings. JSON*e tenere presente che include già quanto segue:

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

Nella riga evidenziata sopra, modificare il nome del database da **_CHANGE_ME** al nome del database.

## <a name="summary"></a>Summary

ASP.NET Core inserisce tutta la logica di avvio per l'applicazione in un unico file, in cui è possibile definire e configurare le dipendenze e i servizi necessari. Sostituisce il file *Web. config* con una funzionalità di configurazione flessibile che può utilizzare una varietà di formati di file, ad esempio JSON, e variabili di ambiente.
