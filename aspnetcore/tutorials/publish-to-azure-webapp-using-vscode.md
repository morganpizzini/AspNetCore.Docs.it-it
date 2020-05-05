---
title: Pubblicare un'app ASP.NET Core in Azure con Visual Studio Code
author: rick-anderson
description: Informazioni su come pubblicare un'app ASP.NET Core in Servizio app di Azure con Visual Studio Code
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: fde0290020e08701bd19b72ebea0e2739462b232
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767844"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Pubblicare un'app ASP.NET Core in Azure con Visual Studio Code

Di [Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Per risolvere un problema di distribuzione del Servizio app di Azure, vedere <xref:test/troubleshoot-azure-iis>.

## <a name="intro"></a>Introduzione

Con questa esercitazione si apprenderà come creare un'applicazione ASP.Net Core MVC e distribuirla all'interno di Visual Studio Code.

## <a name="set-up"></a>Configurare

- Aprire un [account Azure gratuito](https://azure.microsoft.com/free/dotnet/) se non è già disponibile un account.
- Installa [.NET Core SDK](https://dotnet.microsoft.com/download)
- Installa [Visual Studio Code](https://code.visualstudio.com/Download)
  - Installare l'[estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) in Visual Studio Code
  - Installare l' [estensione del servizio app Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) per Visual Studio Code e configurarla prima di procedere

## <a name="create-an-aspnet-core-mvc-project"></a>Creare un progetto ASP.NET Core MVC

Usando un terminale passare alla cartella in cui si vuole creare il progetto e usare il comando seguente:

```dotnetcli
dotnet new mvc
```

Si otterrà una struttura di cartelle simile alla seguente:

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a>Aprire il progetto con Visual Studio Code

Dopo aver creato il progetto, è possibile aprirlo con Visual Studio Code usando una delle opzioni seguenti:

### <a name="through-the-command-line"></a>Tramite la riga di comando

Usare il comando seguente all'interno della cartella in cui è stato creato il progetto:

```cmd
> code .
```

Se il comando seguente non funziona, controllare se l'installazione è configurata correttamente facendo riferimento alle informazioni in [questo collegamento](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).

### <a name="through-visual-studio-code-interface"></a>Tramite l'interfaccia di Visual Studio Code

- Aprire Visual Studio Code
- Nel menu selezionare `File > Open Folder`
- Selezionare la radice della cartella in cui è stato creato il progetto MVC

All'apertura della cartella del progetto verrà visualizzato un messaggio che informa che gli asset necessari per compilare ed eseguire il debug risultano mancanti. Accettare il supporto per aggiungerli.

![Interfaccia di Visual Studio Code con il progetto caricato](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

Verrà creata una cartella `.vscode` nella struttura del progetto. Tale cartella conterrà i file seguenti:

```cmd
launch.json
tasks.json
```

Questi sono file di utilità che consentono di compilare l'app Web .NET Core e di eseguirne il debug.

## <a name="run-the-app"></a>Eseguire l'app

Prima della distribuzione dell'app in Azure, assicurarsi che venga eseguita correttamente nel computer locale.

- Premere F5 per eseguire il progetto

L'app Web verrà eseguita in una nuova scheda del browser predefinito. Potrebbe essere visualizzato un avviso per la privacy subito dopo aver avviato l'app. L'avviso viene visualizzato perché l'app verrà avviata tramite HTTP e HTTPS e passerà all'endpoint HTTPS per impostazione predefinita.

![Avviso per la privacy durante il debug dell'app in locale](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

Per mantenere la sessione di debug, fare clic su `Advanced` e quindi su `Continue to localhost (unsafe)`.

## <a name="generate-the-deployment-package-locally"></a>Generare il pacchetto di distribuzione in locale

- Aprire il terminale di Visual Studio Code
- Usare il comando seguente per generare un pacchetto `Release` in una sottocartella denominata `publish`:
  - `dotnet publish -c Release -o ./publish`
- Verrà creata una nuova cartella `publish` nella struttura del progetto

![Struttura della cartella di pubblicazione](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Eseguire la pubblicazione nel servizio app di Azure

Sfruttando l'estensione del servizio app di Azure per Visual Studio Code, seguire questa procedura per pubblicare il sito Web direttamente nel servizio app di Azure.

### <a name="if-youre-creating-a-new-web-app"></a>Se si crea una nuova app Web

- Fare clic con il pulsante destro del mouse sulla cartella `publish` e scegliere `Deploy to Web App...`.
- Selezionare la sottoscrizione in cui si vuole creare l'app Web
- Selezionare `Create New Web App`
- Immettere un nome per l'app Web

L'estensione creerà la nuova app Web e avvierà automaticamente la distribuzione del pacchetto in tale app. Al termine della distribuzione, fare clic su `Browse Website` per convalidare la distribuzione.

![Messaggio di distribuzione riuscita](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

Quando si fa clic su `Browse Website`, si passerà a tale sito tramite il browser predefinito:

![Nuova app Web distribuita correttamente](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>Per la distribuzione in un'app Web esistente

- Fare clic con il pulsante destro del mouse sulla cartella `publish` e scegliere `Deploy to Web App...`.
- Selezionare la sottoscrizione in cui risiede l'app Web esistente
- Selezionare l'app Web nell'elenco
- Visual Studio Code chiederà se si vuole sovrascrivere il contenuto esistente. Fare clic su `Deploy` per confermare

L'estensione distribuirà il contenuto aggiornato nell'app Web. Al termine, fare clic su `Browse Website` per convalidare la distribuzione.

![App Web esistente distribuita correttamente](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>Passaggi successivi

- [Creare la prima pipeline di Azure DevOps](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Servizio app di Azure](/azure/app-service/app-service-web-overview)
- [Gruppi di risorse di Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
