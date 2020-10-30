---
title: Usare il modello di progetto per React con ASP.NET Core
author: SteveSandersonMS
description: Informazioni su come iniziare a usare il modello di progetto per applicazioni a pagina singola di ASP.NET Core per React e create-react-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
no-loc:
- appsettings.json
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
uid: spa/react
ms.openlocfilehash: 6c3539d96329489189f49a3af0c718791824be6b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054450"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>Usare il modello di progetto per React con ASP.NET Core

Il modello di progetto aggiornato per React fornisce un ottimo punto di partenza per le app ASP.NET Core che usano le convenzioni React e [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) per implementare un'interfaccia utente avanzata sul lato client.

Il modello è equivalente alla creazione di un progetto ASP.NET Core che opera come un back-end API e un progetto React CRA standard che opera come un'interfaccia utente, ma con la praticità di ospitare entrambi in un singolo progetto di app, che può essere creato e pubblicato come una singola unità.

Il modello di progetto React non è progettato per il rendering lato server (SSR). Per SSR con React e Node.js, prendere in considerazione [Next.js](https://github.com/zeit/next.js/) o [Razzle](https://github.com/jaredpalmer/razzle).

## <a name="create-a-new-app"></a>Creare una nuova app

Se ASP.NET Core 2.1 è installato, non è necessario installare il modello di progetto React.

Creare un nuovo progetto da un prompt dei comandi usando il comando `dotnet new react` in una directory vuota. Ad esempio, i comandi seguenti creano l'app in una directory *my-new-app* e passano a tale directory:

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

Eseguire l'app da Visual Studio o dall'interfaccia della riga di comando di .NET Core:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Aprire il file con estensione *csproj* generato ed eseguire l'app come di consueto da tale posizione.

Alla prima esecuzione, il processo di compilazione ripristina le dipendenze di npm. Tale operazione può richiedere alcuni minuti. Le compilazioni successive sono molto più veloci.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Assicurarsi di disporre di una variabile di ambiente denominata `ASPNETCORE_Environment` con valore `Development`. In Windows (in prompt non PowerShell) eseguire `SET ASPNETCORE_Environment=Development`. In Linux o Mac OS eseguire `export ASPNETCORE_Environment=Development`.

Eseguire [dotnet build](/dotnet/core/tools/dotnet-build) per verificare che l'app venga compilata correttamente. Alla prima esecuzione, il processo di compilazione ripristina le dipendenze di npm. Tale operazione può richiedere alcuni minuti. Le compilazioni successive sono molto più veloci.

Eseguire [dotnet run](/dotnet/core/tools/dotnet-run) per avviare l'app.

---

Il modello di progetto crea un'app ASP.NET Core e un'app React. L'app ASP.NET Core è destinata all'uso per l'accesso ai dati, l'autorizzazione e altri elementi sul lato server. L'app React, disponibile nella sottodirectory *ClientApp* , è destinata all'uso per tutti gli aspetti relativi all'interfaccia utente.

## <a name="add-pages-images-styles-modules-etc"></a>Aggiungere pagine, immagini, stili, moduli e così via.

La directory *ClientApp* è un'app React CRA standard. Per altre informazioni, vedere la [documentazione ufficiale di CRA](https://create-react-app.dev/docs/getting-started/).

Vi sono piccole differenze tra l'app React creata tramite questo modello e quella creata tramite CRA stesso. Le funzionalità dell'app restano comunque invariate. L'app creata tramite il modello contiene un layout basato su [bootstrap](https://getbootstrap.com/) e un esempio di routing di base.

## <a name="install-npm-packages"></a>Installa nuovi pacchetti npm

Per installare i pacchetti npm di terze parti, usare un prompt dei comandi nella sottodirectory *ClientApp* . Ad esempio:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Pubblicare e distribuire

In fase di sviluppo, l'app viene eseguita in una modalità ottimizzata per gli sviluppatori. Ad esempio, i bundle JavaScript includono il mapping di origine, in modo da poter visualizzare il codice sorgente originale durante il debug. L'app controlla le modifiche dei file JavaScript, HTML e CSS su disco, quindi esegue automaticamente la ricompilazione e il ricaricamento quando rileva modifiche dei file.

Nell'ambiente di produzione usare una versione dell'app ottimizzata per le prestazioni. Questo comportamento è configurato per l'esecuzione automatica. Quando si esegue la pubblicazione, la configurazione della build genera una compilazione minimizzata con transpile del codice sul lato client. A differenza della build di sviluppo, la build di produzione non richiede l'installazione di Node.js nel server.

È possibile usare i [metodi standard di hosting e distribuzione di ASP.NET Core](xref:host-and-deploy/index).

## <a name="run-the-cra-server-independently"></a>Eseguire il server CRA in modo indipendente

Il progetto è configurato in modo da avviare la propria istanza del server di sviluppo CRA in background all'avvio dell'app ASP.NET Core in modalità di sviluppo. Ciò risulta utile in quanto evita di dover eseguire manualmente un server distinto.

Questa configurazione predefinita presenta tuttavia uno svantaggio. Ogni volta che si modifica il codice C# ed è necessario riavviare l'app ASP.NET Core, il server CRA viene riavviato. Per avviare il backup sono necessari alcuni secondi. Se si apportano frequentemente modifiche al codice C# e non si vuole attendere il riavvio del server CRA, eseguire il server CRA esternamente, in modo indipendente dal processo ASP.NET Core. A tale scopo, procedere nel seguente modo:

1. Aggiungere un file con *estensione ENV* alla sottodirectory *ClientApp* con l'impostazione seguente:

    ```
    BROWSER=none
    ```

    In questo modo si impedisce l'apertura del Web browser all'avvio esterno del server CRA.

2. In un prompt dei comandi passare alla sottodirectory *ClientApp* e avviare il server di sviluppo CRA:

    ```console
    cd ClientApp
    npm start
    ```

3. Modificare l'app ASP.NET Core in modo da usare l'istanza del server CRA esterno anziché avviarne una autonomamente. Nella classe *Startup* sostituire la chiamata `spa.UseReactDevelopmentServer` con quanto segue:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

All'avvio dell'app ASP.NET Core, questa non avvierà un server CRA. Verrà invece usata l'istanza che è stata avviata manualmente. Ciò consente di velocizzare l'avvio e il riavvio. Non è più necessario attendere che l'app React venga ricompilata ogni volta.

> [!IMPORTANT]
> "Il rendering lato server" non è una funzionalità supportata di questo modello. L'obiettivo di questo modello è soddisfare la parità con "create-React-app". Di conseguenza, gli scenari e le funzionalità non inclusi in un progetto "create-React-app" (ad esempio, SSR) non sono supportati e vengono lasciati come esercizio per l'utente.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authentication/identity/spa>
