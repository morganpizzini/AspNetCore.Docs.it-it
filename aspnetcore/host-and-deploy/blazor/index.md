---
title: Ospitare e distribuire ASP.NET CoreBlazor
author: guardrex
description: Scopri come ospitare Blazor e distribuire app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434265"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hosting e distribuzione di ASP.NET Core Blazor

Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Pubblicare l'app

Le app vengono pubblicate per la distribuzione nella configurazione per il rilascio.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Selezionare **Compilazione** > **Pubblicazione ,APPLICATION dalla** barra di spostamento.
1. Selezionare la *destinazione di pubblicazione*. Per pubblicare in locale, selezionare **Cartella**.
1. Accettare il percorso predefinito nel campo **Scegliere una cartella** oppure specificarne uno diverso. Fare clic sul pulsante **Pubblica**.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Usare il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) per pubblicare l'app con una configurazione per il rilascio:

```dotnetcli
dotnet publish -c Release
```

---

La pubblicazione dell'app attiva un [ripristino](/dotnet/core/tools/dotnet-restore) delle dipendenze del progetto e [compila](/dotnet/core/tools/dotnet-build) il progetto prima di creare gli asset per la distribuzione. Come parte del processo di compilazione, vengono rimossi gli assembly e i metodi non usati per ridurre le dimensioni del download e i tempi di caricamento dell'app.

Posizioni di pubblicazione:

* BlazorAssembly Web
  * L'app è autonoma &ndash; pubblicata nella cartella */bin/Release/'TARGET FRAMEWORK'/publish/wwwroot.* Per distribuire l'app come sito statico, copiare il contenuto della cartella *wwwroot* nell'host del sito statico.
  * Hosted &ndash; L'app WebAssembly del client Blazor viene pubblicata nella cartella */bin/Release/* Distribuire il contenuto della cartella di *pubblicazione* nell'host.
* BlazorServer &ndash; L'app viene pubblicata nella cartella */bin/Release/'TARGET FRAMEWORK'/publish.* Distribuire il contenuto della cartella di *pubblicazione* nell'host.

Gli asset nella cartella vengono distribuiti nel server Web. La distribuzione potrebbe essere un processo manuale o automatizzato a seconda degli strumenti di sviluppo in uso.

## <a name="app-base-path"></a>Percorso di base dell'app

Il *percorso di base dell'app* è il percorso dell'URL radice dell'app. Si consideri il Blazor seguente ASP.NET app core e sotto-app:

* L'app ASP.NET `MyApp`Core è denominata:
  * L'app risiede fisicamente in *d:/MyApp*.
  * Le richieste `https://www.contoso.com/{MYAPP RESOURCE}`vengono ricevute all'indirizzo .
* Blazor Un'app `CoolApp` denominata è `MyApp`un'app secondaria di:
  * La sotto-app risiede fisicamente in *d:/MyApp/CoolApp*.
  * Le richieste `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`vengono ricevute all'indirizzo .

Senza specificare la `CoolApp`configurazione aggiuntiva per , la sotto-app in questo scenario non ha alcuna conoscenza di dove risiede sul server. Ad esempio, l'app non può costruire URL relativi corretti alle relative risorse senza `/CoolApp/`sapere che si trova nel percorso URL relativo.

Per fornire la Blazor configurazione per il `https://www.contoso.com/CoolApp/`percorso `<base>` di `href` base dell'app di , l'attributo del tagBlazor viene impostato sul percorso radice relativo nel file *Pages/_Host.cshtml* (Server) o nel file *di wwwroot/index.html* (WebAssembly):Blazor

```html
<base href="/CoolApp/">
```

BlazorLe app server impostano inoltre il <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> percorso di base lato `Startup.Configure`server chiamando nella pipeline di richiesta dell'app:

```csharp
app.UsePathBase("/CoolApp");
```

Fornendo il percorso URL relativo, un componente che non si fa nella directory radice può costruire URL relativi al percorso radice dell'app. I componenti a diversi livelli della struttura di directory possono creare collegamenti ad altre risorse in posizioni in tutta l'app. Il percorso di base dell'app viene `href` usato anche per intercettare i collegamenti ipertestuali selezionati in cui la destinazione del collegamento si trova all'interno dello spazio URI del percorso di base dell'app. Il Blazor router gestisce la navigazione interna.

In molti scenari di hosting, il percorso URL relativo dell'app è la radice dell'app. In questi casi, il percorso di base dell'URL relativo dell'app è una barra (`<base href="/" />`), che è la configurazione predefinita per un'app. Blazor In altri scenari di hosting, ad esempio gitHub Pages e i sottoapp IIS, il percorso di base dell'app deve essere impostato sul percorso URL relativo del server dell'app.

Per impostare il percorso di `<base>` base dell'app, aggiornare il tag `<head>` all'interno degli elementiBlazor tag del file *Pages/_Host.cshtml* (Server)Blazor o del file di *wwwroot/index.html* (WebAssembly). Imposta `href` il valore `/{RELATIVE URL PATH}/` dell'attributo su (la barra finale è obbligatoria), dove `{RELATIVE URL PATH}` è il percorso URL relativo completo dell'app.

Per Blazor un'app WebAssembly con un percorso URL `<base href="/CoolApp/">`relativo non radice (ad esempio, ), l'app non riesce a trovare le risorse *quando viene eseguita in locale.* Per risolvere questo problema durante sviluppo e test locali, è possibile specificare un argomento *base del percorso* corrispondente al valore `href` del tag `<base>` in fase di esecuzione. Non includere una barra finale. Per passare l'argomento di base del `dotnet run` percorso durante l'esecuzione dell'app in locale, esegui il comando dalla directory dell'app con l'opzione: `--pathbase`

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Per Blazor un'app WebAssembly con `/CoolApp/` un`<base href="/CoolApp/">`percorso URL relativo di ( ), il comando è:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

L'app Blazor WebAssembly risponde `http://localhost:port/CoolApp`localmente in corrispondenza di .

## <a name="deployment"></a>Distribuzione

Per indicazioni sulla distribuzione, vedere gli argomenti seguenti:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
