---
title: Eseguire la migrazione da ASP.NET ad ASP.NET Core 2.0
author: isaac2004
description: Ricevere indicazioni per la migrazione di applicazioni ASP.NET MVC o API Web esistenti a ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc2
ms.openlocfilehash: 98b93731d40b47be8d7d5050afc7c74d25a7e1dc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776272"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a>Eseguire la migrazione da ASP.NET ad ASP.NET Core 2.0

Di [Isaac Levin](https://isaaclevin.com)

Questo articolo offre una guida di riferimento per la migrazione delle applicazioni ASP.NET ad ASP.NET Core 2.0.

## <a name="prerequisites"></a>Prerequisiti

Installare **uno** dei seguenti download da [.NET: Windows](https://dotnet.microsoft.com/download):

* .NET Core SDK
* Visual Studio per Windows
  * **ASP.NET e carico di lavoro di sviluppo Web**
  * Carico di lavoro di **sviluppo multipiattaforma .NET Core**

## <a name="target-frameworks"></a>Framework di destinazione

I progetti di ASP.NET Core 2.0 offrono agli sviluppatori la flessibilità necessaria per scegliere .NET Core, .NET Framework o entrambi come destinazione. Vedere [Scelta di .NET Core o .NET Framework per le app server](/dotnet/standard/choosing-core-framework-server) per determinare quale framework di destinazione è più appropriato.

Quando la destinazione è .NET Framework, i progetti devono fare riferimento a singoli pacchetti NuGet.

La scelta di .NET Core come destinazione consente di eliminare numerosi riferimenti espliciti ai pacchetti, grazie al [metapacchetto](xref:fundamentals/metapackage) di ASP.NET 2.0 Core. Installare il metapacchetto `Microsoft.AspNetCore.All` nel progetto:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

Quando si usa il metapacchetto, con l'app non viene distribuito alcun pacchetto a cui si fa riferimento nel metapacchetto. L'archivio di runtime di .NET Core include questi asset, che vengono precompilati per migliorare le prestazioni. Per <xref:fundamentals/metapackage> ulteriori informazioni, vedere.

## <a name="project-structure-differences"></a>Differenze di struttura del progetto

Il formato di file *CSPROJ* è stato semplificato in ASP.NET Core. Alcune modifiche importanti includono:

* L'inclusione esplicita dei file non è necessaria affinché i file vengano considerati parte del progetto. In questo modo si riduce il rischio di conflitti di merge XML quando si lavora con team di grandi dimensioni.
* Non sono presenti riferimenti basati su GUID ad altri progetti e questo migliora la leggibilità dei file.
* Il file può essere modificato senza scaricarlo in Visual Studio:

  ![Modificare l'opzione CSPROJ del menu di scelta rapida in Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Sostituzione di file Global.asax

In ASP.NET Core è stato introdotto un nuovo meccanismo per l'avvio automatico delle app. Il punto di ingresso per le applicazioni ASP.NET è il file *Global.asax*. Attività quali la configurazione della route e le registrazioni di area e filtro vengono gestite nel file *Global.asax*.

[!code-csharp[](samples/globalasax-sample.cs)]

Con questo approccio l'applicazione e il server a cui viene distribuita vengono accoppiati in un modo che interferisce con l'implementazione. Al fine di disaccoppiare gli elementi, è stata introdotta la funzionalità [OWIN](https://owin.org/) che offre un modo più semplice di usare più framework insieme. OWIN offre una pipeline per aggiungere solo i moduli necessari. L'ambiente host accetta una funzione di [avvio](xref:fundamentals/startup) per configurare i servizi e la pipeline delle richieste dell'applicazione. `Startup` registra un set di middleware con l'applicazione. Per ogni richiesta, l'applicazione chiama ognuno dei componenti middleware con il puntatore iniziale di un elenco collegato a un set esistente di gestori. Ogni componente middleware può aggiungere uno o più gestori alla pipeline di gestione delle richieste. Questa operazione viene eseguita restituendo un riferimento al gestore che rappresenta il nuovo inizio dell'elenco. Ogni gestore è responsabile della memorizzazione e della chiamata del gestore successivo nell'elenco. Con ASP.NET Core, il punto di ingresso a un'applicazione è `Startup` e non esiste più una dipendenza da *Global.asax*. Quando si usa OWIN con .NET Framework, usare una pipeline simile alla seguente:

[!code-csharp[](samples/webapi-owin.cs)]

Ciò consente di configurare le route predefinite e impostare come predefinito XmlSerialization per Json. Aggiungere altro middleware a questa pipeline in base alle esigenze, ad esempio caricamento di servizi, impostazioni di configurazione, file statici e così via.

ASP.NET Core usa un approccio simile, ma non si basa su OWIN per gestire la voce. L'operazione viene invece eseguita usando il metodo *Program.cs* `Main` (simile alle applicazioni console) e `Startup` viene caricato da tale posizione.

[!code-csharp[](samples/program.cs)]

`Startup` deve includere un metodo `Configure`. In `Configure` aggiungere il middleware necessario alla pipeline. Nell'esempio seguente, estratto dal modello di sito Web predefinito, vengono usati vari metodi di estensione per configurare la pipeline con il supporto per:

* [BrowserLink](https://vswebessentials.com/features/browserlink)
* Pagine di errore
* File statici
* ASP.NET Core MVC
* Identity

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

L'host e applicazione sono stati disaccoppiati e questo offre la possibilità di passare a una piattaforma diversa in futuro.

Per un riferimento più approfondito a ASP.NET Core avvio e middleware, vedere <xref:fundamentals/startup>.

## <a name="storing-configurations"></a>Archiviazione delle configurazioni

ASP.NET supporta le impostazioni di archiviazione. Tali impostazioni vengono usate, ad esempio, per supportare l'ambiente in cui vengono distribuite le applicazioni. Una prassi comune era archiviare tutte le coppie chiave-valore personalizzate nella sezione `<appSettings>` del file *Web.config*:

[!code-xml[](samples/webconfig-sample.xml)]

Le applicazioni leggono queste impostazioni usando la raccolta `ConfigurationManager.AppSettings` nello spazio dei nomi `System.Configuration`:

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core è in grado di archiviare i dati di configurazione per l'applicazione in tutti i file e di caricarli come parte dell'avvio automatico del middleware. Il file predefinito usato nei modelli di progetto è *appSettings.json*:

[!code-json[](samples/appsettings-sample.json)]

Il caricamento del file in un'istanza di `IConfiguration` all'interno dell'applicazione viene eseguito in *Startup.cs*:

[!code-csharp[](samples/startup-builder.cs)]

L'app legge da `Configuration` per ottenere le impostazioni:

[!code-csharp[](samples/read-appsettings.cs)]

Sono disponibili estensioni di questo approccio che aumentano l'efficacia del processo, ad esempio l'uso dell'[inserimento delle dipendenze](xref:fundamentals/dependency-injection) per caricare un servizio con questi valori. L'approccio con inserimento delle dipendenze offre un set fortemente tipizzato di oggetti di configurazione.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

**Nota:** Per un riferimento più approfondito alla configurazione di ASP.NET Core, vedere <xref:fundamentals/configuration/index>.

## <a name="native-dependency-injection"></a>Inserimento delle dipendenze nativo

Un obiettivo importante nella compilazione di applicazioni scalabili di grandi dimensioni è l'accoppiamento libero di componenti e servizi. L' [inserimento di dipendenze](xref:fundamentals/dependency-injection) è una tecnica comune per ottenere questo aspetto ed è un componente nativo di ASP.NET Core.

Nelle applicazioni ASP.NET gli sviluppatori si affidano a una libreria di terze parti per implementare l'inserimento delle dipendenze. Una di queste librerie è [Unity](https://github.com/unitycontainer/unity) e fa parte di Modelli e procedure Microsoft.

Un esempio di configurazione dell'inserimento delle dipendenze con Unity consiste nell' `IDependencyResolver` implementazione `UnityContainer`di che esegue il wrapping di un:

[!code-csharp[](samples/sample8.cs)]

Creare un'istanza di `UnityContainer`, registrare il servizio e impostare il sistema di risoluzione delle dipendenze di `HttpConfiguration` sulla nuova istanza di `UnityResolver` per il contenitore:

[!code-csharp[](samples/sample9.cs)]

Inserire `IProductRepository` dove necessario:

[!code-csharp[](samples/sample5.cs)]

Poiché l' `Startup.ConfigureServices`inserimento delle dipendenze fa parte di ASP.NET Core, è possibile aggiungere il servizio in:

[!code-csharp[](samples/configure-services.cs)]

Il repository può essere inserito in qualsiasi posizione, analogamente a Unity.

Per ulteriori informazioni sull'inserimento delle dipendenze in ASP.NET Core <xref:fundamentals/dependency-injection>, vedere.

## <a name="serving-static-files"></a>Gestione dei file statici

Una parte importante dello sviluppo Web è la possibilità di distribuire asset statici sul lato client. Gli esempi più comuni di file statici sono HTML, CSS, Javascript e immagini. Questi file devono essere salvati nella posizione di pubblicazione dell'app (o della rete CDN) con riferimenti che ne consentano il caricamento da parte di una richiesta. Questo processo è stato modificato in ASP.NET Core.

In ASP.NET i file statici vengono archiviati in directory diverse e viene fatto riferimento ai file nelle viste.

In ASP.NET Core i file statici vengono archiviati nella radice Web (*&lt;/wwwroot radice&gt;del contenuto*), a meno che non sia configurato diversamente. I file vengono caricati nella pipeline delle richieste chiamando il metodo di estensione `UseStaticFiles` da `Startup.Configure`:

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

**Nota:** se la destinazione è .NET Framework, installare il pacchetto NuGet `Microsoft.AspNetCore.StaticFiles`.

Ad esempio, un asset immagine nella cartella *wwwroot/images* è accessibile al browser in corrispondenza di una posizione come `http://<app>/images/<imageFileName>`.

**Nota:** Per informazioni più dettagliate sulla conservazione dei file statici in ASP.NET Core, vedere <xref:fundamentals/static-files>.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Portabilità in .NET Core - Librerie](/dotnet/core/porting/libraries)
