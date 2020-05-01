---
title: Ospitare e distribuire ASP.NET CoreBlazor
author: guardrex
description: Scopri come ospitare e distribuire Blazor le app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 993c1e49aed2efb4283753ab184506e5ae33a3b2
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604818"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hosting e distribuzione di ASP.NET Core Blazor

Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Pubblicare l'app

Le app vengono pubblicate per la distribuzione nella configurazione per il rilascio.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Selezionare **Compila** > **pubblicazione {applicazione}** dalla barra di spostamento.
1. Selezionare la *destinazione di pubblicazione*. Per pubblicare in locale, selezionare **Cartella**.
1. Accettare il percorso predefinito nel campo **Scegliere una cartella** oppure specificarne uno diverso. Fare clic sul pulsante **Pubblica**.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Selezionare **Compila** > **pubblicazione in cartella**.
1. Confermare la cartella per ricevere gli asset pubblicati e selezionare **pubblica**.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Usare il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) per pubblicare l'app con una configurazione per il rilascio:

```dotnetcli
dotnet publish -c Release
```

---

La pubblicazione dell'app attiva un [ripristino](/dotnet/core/tools/dotnet-restore) delle dipendenze del progetto e [compila](/dotnet/core/tools/dotnet-build) il progetto prima di creare gli asset per la distribuzione. Come parte del processo di compilazione, vengono rimossi gli assembly e i metodi non usati per ridurre le dimensioni del download e i tempi di caricamento dell'app.

Percorsi di pubblicazione:

* BlazorWebassembly
  * Standalone &ndash; l'app viene pubblicata nella cartella */bin/Release/{Target Framework}/Publish/wwwroot* Per distribuire l'app come sito statico, copiare il contenuto della cartella *wwwroot* nell'host del sito statico.
  * Ospitata &ndash; l' Blazor app webassembly client viene pubblicata nella cartella */bin/Release/{Target Framework}/Publish/wwwroot* dell'app Server, insieme a qualsiasi altra risorsa Web statica dell'app Server. Distribuire il contenuto della cartella di *pubblicazione* nell'host.
* BlazorServer &ndash; l'app viene pubblicata nella cartella */bin/Release/{Target Framework}/Publish* Distribuire il contenuto della cartella di *pubblicazione* nell'host.

Gli asset nella cartella vengono distribuiti nel server Web. La distribuzione potrebbe essere un processo manuale o automatizzato a seconda degli strumenti di sviluppo in uso.

## <a name="app-base-path"></a>Percorso di base dell'app

Il *percorso di base dell'app* è il percorso dell'URL radice dell'app. Si considerino i seguenti ASP.NET Core Blazor app e l'app secondaria:

* L'app ASP.NET Core è denominata `MyApp`:
  * L'app risiede fisicamente in *d:/MyApp*.
  * Le richieste vengono ricevute all'indirizzo `https://www.contoso.com/{MYAPP RESOURCE}`.
* Un' Blazor app denominata `CoolApp` è un'app secondaria di `MyApp`:
  * La Sub-App risiede fisicamente in *d:/MyApp/CoolApp*.
  * Le richieste vengono ricevute all'indirizzo `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.

Se non si specifica una `CoolApp`configurazione aggiuntiva per, la sottoapp in questo scenario non è a conoscenza della posizione in cui risiede nel server. Ad esempio, l'app non può costruire URL relativi corretti per le risorse senza sapere che risiede nel percorso `/CoolApp/`URL relativo.

Per fornire la configurazione per Blazor il percorso di base dell' `https://www.contoso.com/CoolApp/`app, `<base>` l' `href` attributo del tag viene impostato sul percorso radice relativo nel file *pages/_Host. cshtml* (Blazor Server) o *wwwroot/index.html* (Blazor webassembly):

```html
<base href="/CoolApp/">
```

BlazorAnche le app Server impostano il percorso di base sul <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> lato server chiamando nella pipeline delle richieste `Startup.Configure`dell'app di:

```csharp
app.UsePathBase("/CoolApp");
```

Fornendo il percorso URL relativo, un componente che non si trova nella directory radice può costruire URL relativi al percorso radice dell'app. I componenti a livelli diversi della struttura di directory possono creare collegamenti ad altre risorse in posizioni all'interno dell'app. Il percorso di base dell'app viene usato anche per intercettare i collegamenti ipertestuali selezionati in cui la `href` destinazione del collegamento si trova nello spazio URI del percorso di base dell'app. Il Blazor router gestisce la navigazione interna.

In molti scenari di hosting il percorso URL relativo dell'app è la radice dell'app. In questi casi, il percorso di base dell'URL relativo dell'app è una barra`<base href="/" />`(), ovvero la configurazione predefinita per un' Blazor app. In altri scenari di hosting, ad esempio pagine di GitHub e sottoapp di IIS, il percorso di base dell'app deve essere impostato sul percorso URL relativo del server dell'app.

Per impostare il percorso di base dell'app, aggiornare `<base>` il tag negli `<head>` elementi tag del file *pages/_Host. cshtml* (Blazor Server) o *wwwroot/index.html* (Blazor webassembly). Impostare il `href` valore dell'attributo `/{RELATIVE URL PATH}/` su (la barra finale è obbligatoria), dove `{RELATIVE URL PATH}` è il percorso URL relativo completo dell'app.

Per un' Blazor app webassembly con un percorso URL relativo non radice (ad esempio, `<base href="/CoolApp/">`), l'app non riesce a trovare le proprie risorse quando viene *eseguita localmente*. Per risolvere questo problema durante sviluppo e test locali, è possibile specificare un argomento *base del percorso* corrispondente al valore `href` del tag `<base>` in fase di esecuzione. Non includere una barra finale. Per passare l'argomento di base del percorso quando si esegue l'app in `dotnet run` locale, eseguire il comando dalla directory dell' `--pathbase` app con l'opzione:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Per un' Blazor app webassembly con un percorso URL relativo `/CoolApp/` (`<base href="/CoolApp/">`), il comando è:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

L' Blazor app webassembly risponde localmente all'indirizzo `http://localhost:port/CoolApp`.

## <a name="deployment"></a>Distribuzione

Per indicazioni sulla distribuzione, vedere gli argomenti seguenti:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
