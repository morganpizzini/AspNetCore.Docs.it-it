---
title: Usare gli analizzatori dell'API Web
author: pranavkm
description: Informazioni sul pacchetto degli analizzatori di API Web ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
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
uid: web-api/advanced/analyzers
ms.openlocfilehash: cf0415e7d72e21a48db8bbeb4540f05e0b0a4198
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057921"
---
# <a name="use-web-api-analyzers"></a>Usare gli analizzatori dell'API Web

ASP.NET Core 2,2 e versioni successive fornisce un pacchetto di analizzatori MVC progettato per l'uso con i progetti API Web. Gli analizzatori funzionano con i controller annotati con <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> , durante la compilazione in [convenzioni API Web](xref:web-api/advanced/conventions).

Il pacchetto degli analizzatori informa l'utente di qualsiasi azione del controller che:

* Restituisce un codice di stato non dichiarato.
* Restituisce un risultato non dichiarato di esito positivo.
* Documenta un codice di stato che non viene restituito.
* Include un controllo di convalida del modello esplicito.

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>Fare riferimento al pacchetto dell'analizzatore

In ASP.NET Core 3,0 o versioni successive, gli analizzatori sono inclusi nel .NET Core SDK. Per abilitare l'analizzatore nel progetto, includere la `IncludeOpenAPIAnalyzers` proprietà nel file di progetto:

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a>Installazione del pacchetto

Installare il pacchetto NuGet [Microsoft. AspNetCore. Mvc. API. Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) con uno degli approcci seguenti:

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dalla finestra **Console di Gestione pacchetti** :
  * Passare a **Visualizza** > **altre** > **console di gestione pacchetti** di Windows.
  * Passare alla directory che contiene il file *ApiConventions.csproj* .
  * Eseguire il comando seguente:

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Fare clic con il pulsante destro del mouse sulla cartella *pacchetti* in **riquadro della soluzione** > **Aggiungi pacchetti...** .
* Impostare l'elenco a discesa di **origine** della finestra **Aggiungi pacchetti** su "NuGet.org".
* Immettere "Microsoft.AspNetCore.Mvc.Api.Analyzers" nella casella di ricerca.
* Selezionare il pacchetto "Microsoft.AspNetCore.Mvc.Api.Analyzers" nel riquadro dei risultati e fare clic su **Aggiungi pacchetto** .

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Eseguire il comando seguente da **Terminale integrato** :

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Eseguire il comando seguente:

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a>Analizzatori per le convenzioni API Web

I documenti OpenAPI contengono i codici di stato e i tipi di risposta che può restituire un'azione. In ASP.NET Core MVC, per documentare un'azione vengono usati attributi come <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> e <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>. <xref:tutorials/web-api-help-pages-using-swagger> viene illustrato in dettaglio la documentazione dell'API Web.

Uno degli analizzatori del pacchetto verifica i controller annotati con <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> e identifica le azioni che non documentano completamente le relative risposte. Si consideri l'esempio seguente:

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

L'azione precedente documenta il tipo restituito HTTP 200 (operazione riuscita) ma non documenta il codice di stato HTTP 404 (operazione non riuscita). L'analizzatore segnala l'assenza di documentazione per il codice di stato HTTP 404 come un avviso. È disponibile un'opzione per risolvere il problema.

![Analizzatore che restituisce un avviso](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
