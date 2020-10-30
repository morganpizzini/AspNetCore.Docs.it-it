---
title: Bundle di Hosting
author: rick-anderson
description: Informazioni su come configurare il bundle di hosting .NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: ecf3dd45575390eee263a275e7f1fb9ec50011bb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058441"
---
# <a name="the-net-core-hosting-bundle"></a>Bundle di hosting .NET Core

Il bundle di hosting di .NET Core è un programma di installazione per il runtime di .NET Core e il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Il bundle consente l'esecuzione delle app ASP.NET Core con IIS.

## <a name="install-the-net-core-hosting-bundle"></a>Installare il bundle di hosting .NET Core

> [!IMPORTANT]
> Se il bundle di hosting viene installato prima di IIS, è necessario riparare l'installazione del bundle. Eseguire di nuovo il programma di installazione del bundle di hosting dopo l'installazione di IIS.
>
> Se il bundle di hosting viene installato dopo l'installazione della versione a 64 bit (x64) di .NET Core, uno o più SDK potrebbero risultare mancanti ([Non sono stati rilevati .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Per risolvere il problema, vedere <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="direct-download-current-version"></a>Download diretto (versione corrente)

Scaricare il programma di installazione mediante il collegamento seguente:

[Programma di installazione del bundle di hosting .NET Core corrente (download diretto)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Versioni precedenti del programma di installazione

Per ottenere una versione precedente del programma di installazione:

1. Passare alla pagina [download di .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .
1. Selezionare la versione desiderata di .NET Core.
1. Nella colonna **Run apps - Runtime** (App di esecuzione - Runtime), trovare la riga della versione di runtime di .NET Core desiderata.
1. Scaricare il programma di installazione usando il collegamento **bundle di hosting** .

> [!WARNING]
> Alcuni programmi di installazione contengono versioni che hanno terminato il loro ciclo di vita e non sono più supportate da Microsoft. Per altre informazioni, vedere i [criteri di supporto](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Installare il bundle di hosting

1. Eseguire il programma di installazione nel server. Quando si esegue il programma di installazione da una shell dei comandi di amministratore sono disponibili i parametri seguenti:

   * `OPT_NO_ANCM=1`: Ignorare l'installazione del modulo ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: Ignorare l'installazione del runtime di .NET Core. Utilizzato quando il server ospita solo le [distribuzioni autosufficienti (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: Ignora l'installazione del Framework condiviso ASP.NET (runtime ASP.NET). Utilizzato quando il server ospita solo le [distribuzioni autosufficienti (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: Ignora l'installazione di Runtime x86. Usare questo parametro se si è certi che non verrà eseguito l'hosting di app a 32 bit. Se non esiste alcuna possibilità che in futuro venga eseguito l'hosting di app sia a 32 che a 64 bit, non usare questo parametro e installare entrambi i runtime.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Disabilitare il controllo per l'utilizzo di una configurazione condivisa di IIS quando la configurazione condivisa ( `applicationHost.config` ) si trova nello stesso computer dell'installazione di IIS. *Disponibile solo per i programmi di installazione di bundler di hosting ASP.NET Core 2.2 o versioni successive.* Per altre informazioni, vedere <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Riavviare il sistema o eseguire i comandi seguenti in una shell dei comandi:

   ```console
   net stop was /y
   net start w3svc
   ```
   Il riavvio di IIS rileva una modifica alla variabile di ambiente di sistema PATH apportata dal programma di installazione.

ASP.NET Core non adotta il comportamento di rollforward per le versioni di patch dei pacchetti di Framework condivisi. Dopo l'aggiornamento del Framework condiviso mediante l'installazione di un nuovo bundle di hosting, riavviare il sistema o eseguire i comandi seguenti in una shell dei comandi:

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> Per informazioni sulla configurazione condivisa di IIS, vedere [Modulo di ASP.NET Core con configurazione condivisa di IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Versione del modulo e log del programma di installazione del bundle di hosting

Per determinare la versione del modulo ASP.NET Core installato:

1. Nel sistema di hosting passare a `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .
1. Individuare il `aspnetcorev2.dll` file.
1. Fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà** dal menu di scelta rapida.
1. Selezionare la scheda **Dettagli** . La versione del **file** e la **versione del prodotto** rappresentano la versione installata del modulo.

I log del programma di installazione del bundle di hosting per il modulo sono disponibili in `C:\Users\%UserName%\AppData\Local\Temp` . Il nome del file è `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , dove il segnaposto `{TIMESTAMP}` è il timestamp del file.
