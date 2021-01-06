---
title: Pubblicare un'app ASP.NET Core in IIS
author: rick-anderson
description: Informazioni su come ospitare un'app ASP.NET Core in un server IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: 0f70b5f12b9097f8710c9641404b3e085968fc3f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753153"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>Pubblicare un'app ASP.NET Core in IIS

Questa esercitazione mostra come ospitare un'app ASP.NET Core in un server IIS.

Questa esercitazione illustra le operazioni seguenti:

> [!div class="checklist"]
> * Installare il bundle di hosting di.NET Core in Windows Server.
> * Creare un sito IIS in Gestione IIS.
> * Distribuire un'app ASP.NET Core.

## <a name="prerequisites"></a>Prerequisiti

* [.NET Core SDK](/dotnet/core/sdk) installato nel computer di sviluppo.
* Windows Server configurato con il ruolo del server **Server Web (IIS)**. Se il server non è configurato per ospitare siti Web con IIS, seguire le istruzioni nella sezione *Configurazione di IIS* dell'articolo <xref:host-and-deploy/iis/index#iis-configuration> e quindi tornare a questa esercitazione.

> [!WARNING]
> **La configurazione di IIS e la sicurezza del sito Web coinvolgono concetti non trattati in questa esercitazione.** Consultare le linee guida per IIS nella [documentazione di Microsoft IIS](https://www.iis.net/) e l'[articolo di ASP.NET Core sull'hosting con IIS](xref:host-and-deploy/iis/index) prima di ospitare app di produzione in IIS.
>
> Gli scenari importanti per l'hosting di IIS non trattati in questa esercitazione includono:
>
> * [Creazione di un hive del Registro di sistema per la protezione dei dati ASP.NET Core](xref:host-and-deploy/iis/advanced#data-protection)
> * [Configurazione dell'elenco di controllo di accesso (ACL) del pool di app](xref:host-and-deploy/iis/advanced#application-pool-identity)
> * Per concentrarsi sui concetti correlati alla distribuzione di IIS, questa esercitazione illustra come distribuire un'app senza sicurezza HTTPS configurata in IIS. Per altre informazioni sull'hosting di un'app abilitata per il protocollo HTTPS, vedere gli argomenti relativi alla sicurezza nella sezione [Risorse aggiuntive](#additional-resources) di questo articolo. Altre indicazioni per l'hosting di app ASP.NET Core sono disponibili nell'articolo <xref:host-and-deploy/iis/index>.

## <a name="install-the-net-core-hosting-bundle"></a>Installare il bundle di hosting .NET Core

Installare il *bundle di hosting .NET Core* nel server IIS. Il bundle installa il runtime di .NET Core, la libreria di .NET Core e il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Il modulo consente l'esecuzione delle app ASP.NET Core dietro IIS.

Scaricare il programma di installazione mediante il collegamento seguente:

[Programma di installazione del bundle di hosting .NET Core corrente (download diretto)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. Eseguire il programma di installazione nel server IIS.

1. Riavviare il server o eseguire `net stop was /y` seguito da `net start w3svc` in una shell dei comandi.

## <a name="create-the-iis-site"></a>Creare il sito IIS

1. Nel server IIS creare una cartella per contenere le cartelle e i file pubblicati dell'app. In un passaggio successivo, il percorso della cartella viene fornito a IIS come percorso fisico dell'app. Per altre informazioni sulla cartella di distribuzione e il layout dei file di un'app, vedere <xref:host-and-deploy/directory-structure>.

1. In Gestione IIS aprire il nodo del server nel pannello **connessioni** . Fare clic con il pulsante destro del mouse sulla cartella **Siti**. Scegliere **Aggiungi sito Web** dal menu di scelta rapida.

1. Specificare un **Nome del sito** e impostare il **Percorso fisico** per la cartella di distribuzione dell'app che è stata creata. Specificare la configurazione in **Binding** e creare il sito Web selezionando **OK**.

   > [!WARNING]
   > Le associazioni con caratteri jolly di livello superiore (`http://*:80/` e `http://+:80`) **non** devono essere usate, poiché possono introdurre vulnerabilità a livello di sicurezza nell'app. Questo concetto vale sia per i caratteri jolly sicuri che vulnerabili. Usare nomi host espliciti al posto di caratteri jolly. L'associazione con caratteri jolly del sottodominio (ad esempio, `*.mysub.com`) non costituisce un rischio per la sicurezza se viene controllato l'intero dominio padre (a differenza di `*.com`, che è vulnerabile). Vedere la [sezione 5.4 di RFC7230](https://tools.ietf.org/html/rfc7230#section-5.4) per altre informazioni.

1. Confermare che l'identità del modello del processo disponga delle autorizzazioni appropriate.

   Se l'identità predefinita del pool di app (**modello**  >  **Identity** di processo) viene modificata da `ApplicationPoolIdentity` a un'altra identità, verificare che la nuova identità disponga delle autorizzazioni necessarie per accedere alla cartella dell'app, al database e ad altre risorse richieste. Ad esempio, il pool di applicazioni richiede l'accesso in lettura e scrittura alle cartelle in cui l'app legge e scrive i file.

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a>Creare un'app di ASP.NET Core Razor pages

Seguire l' <xref:getting-started> esercitazione per creare un' Razor app pagine.

## <a name="publish-and-deploy-the-app"></a>Pubblicare e distribuire l'app

*Pubblicare un'app* significa creare un'app compilata che può essere ospitata da un server. *Distribuire un'app* significa spostare l'app pubblicata in un sistema di hosting. Il passaggio di pubblicazione viene gestito da [.NET Core SDK](/dotnet/core/sdk), mentre la fase di distribuzione può essere gestita tramite vari approcci. Questa esercitazione adotta l'approccio di distribuzione tramite una *cartella*, in cui:
 
* L'app viene pubblicata in una cartella.
* Il contenuto della cartella viene spostato nella cartella del sito IIS, ovvero il **percorso fisico** del sito in Gestione IIS.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica**.
1. Nella finestra di dialogo **Selezionare una destinazione di pubblicazione** selezionare l'opzione di pubblicazione **Cartella**.
1. Impostare il percorso **Cartella o condivisione file**.
   * Se è stata creata una cartella per il sito IIS disponibile nel computer di sviluppo come una condivisione di rete, specificare il percorso della condivisione. L'utente corrente deve avere l'accesso in scrittura per la pubblicazione nella condivisione.
   * Se non si è in grado di eseguire la distribuzione direttamente nella cartella del sito IIS nel server IIS, pubblicare in una cartella su supporti rimovibili e spostare fisicamente l'app pubblicata nella cartella del sito IIS nel server, ovvero il **percorso fisico** del sito in Gestione IIS. Spostare il contenuto della `bin/Release/{TARGET FRAMEWORK}/publish` cartella nella cartella del sito IIS sul server, ovvero il **percorso fisico** del sito in Gestione IIS.
1. Fare clic sul pulsante **Pubblica**.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

1. Da una shell dei comandi pubblicare l'app nella configurazione di versione con il comando [dotnet publish](/dotnet/core/tools/dotnet-publish):

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. Spostare il contenuto della `bin/Release/{TARGET FRAMEWORK}/publish` cartella nella cartella del sito IIS sul server, ovvero il **percorso fisico** del sito in Gestione IIS.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Fare clic con il pulsante destro del mouse sul progetto in **Soluzione** e scegliere **Pubblica** > **Pubblica nella cartella**.
1. Impostare il percorso in **Scegliere una cartella**.
   * Se è stata creata una cartella per il sito IIS disponibile nel computer di sviluppo come una condivisione di rete, specificare il percorso della condivisione. L'utente corrente deve avere l'accesso in scrittura per la pubblicazione nella condivisione.
   * Se non è possibile eseguire la distribuzione direttamente nella cartella del sito IIS nel server IIS, pubblicare in una cartella su un supporto rimovibile e spostare fisicamente l'app pubblicata nella cartella del sito IIS nel server, ovvero il **percorso fisico** del sito in Gestione IIS. Spostare il contenuto della `bin/Release/{TARGET FRAMEWORK}/publish` cartella nella cartella del sito IIS sul server, ovvero il **percorso fisico** del sito in Gestione IIS.
1. Fare clic sul pulsante **Pubblica**.

---

## <a name="browse-the-website"></a>Esplorare il sito Web

L'app è accessibile in un browser dopo la ricezione della prima richiesta. Inviare una richiesta all'app nell'associazione dell'endpoint che è stata stabilita in Gestione IIS per il sito.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Installare il bundle di hosting di.NET Core in Windows Server.
> * Creare un sito IIS in Gestione IIS.
> * Distribuire un'app ASP.NET Core.

Per altre informazioni sull'hosting di app ASP.NET Core in IIS, vedere la panoramica su IIS:

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>Risorse aggiuntive

### <a name="articles-in-the-aspnet-core-documentation-set"></a>Articoli nel set di documentazione di ASP.NET Core

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>Articoli relativi alla distribuzione di app ASP.NET Core

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Pubblicare un'app Web in una cartella usando Visual Studio per Mac](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>Articoli sulla configurazione HTTPS di IIS

* [Configurazione di SSL in Gestione IIS](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [Come configurare SSL in IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>Articoli su IIS e Windows Server

* [Il sito IIS ufficiale di Microsoft](https://www.iis.net/)
* [Libreria di contenuti tecnici di Windows Server](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a>Risorse di distribuzione per amministratori IIS

* [Documentazione di ISS](/iis)
* [Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8) (Introduzione a Gestione IIS in IIS)
* [Distribuzione di applicazioni .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

