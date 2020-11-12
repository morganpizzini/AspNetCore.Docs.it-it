---
title: Telemetria HttpRepl
author: scottaddie
description: Informazioni sui dati di telemetria raccolti dal HttpRepl.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550608"
---
# <a name="httprepl-telemetry"></a>Telemetria HttpRepl

Il [HttpRepl](xref:web-api/http-repl) include una funzionalità di telemetria che raccoglie i dati di utilizzo. È importante che il team di HttpRepl comprenda come viene usato lo strumento in modo che possa essere migliorato.

## <a name="how-to-opt-out"></a>Come rifiutare esplicitamente

La funzionalità di telemetria HttpRepl è abilitata per impostazione predefinita. Per rifiutare esplicitamente la funzionalità di telemetria, impostare la variabile di ambiente `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` su `1` o `true`.

## <a name="disclosure"></a>Divulgazione

Il HttpRepl Visualizza un testo simile al seguente quando si esegue lo strumento per la prima volta. Il testo può variare leggermente a seconda della versione dello strumento in esecuzione. Questa prima esperienza riguarda la modalità di notifica della raccolta dei dati da parte di Microsoft.

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

Per escludere il testo dell'esperienza "prima esecuzione", impostare la `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` variabile di ambiente su `1` o `true` .

## <a name="data-points"></a>Punti dati

La funzionalità di telemetria non:

* Raccogliere dati personali, ad esempio nomi utente, indirizzi di posta elettronica o URL.
* Esegue l'analisi delle richieste o delle risposte HTTP.

I dati vengono inviati in modo sicuro ai server Microsoft e conservati con accesso limitato.

La tutela della privacy è importante per Microsoft. Se si ritiene che la funzionalità di telemetria raccolga dati sensibili o che i dati siano gestiti in modo non sicuro o non appropriato, eseguire una delle azioni seguenti:

* Archiviare un problema nel repository [DotNet/httprepl](https://github.com/dotnet/httprepl/issues) .
* Inviare un messaggio di posta elettronica a [dotnet@microsoft.com](mailto:dotnet@microsoft.com) per l'analisi.

La funzionalità di telemetria raccoglie i dati seguenti.

| Versioni di .NET SDK | Dati |
|--------------|------|
| >= 5,0        | Timestamp della chiamata. |
| >= 5,0        | Indirizzo IP a tre ottetti usato per determinare la posizione geografica. |
| >= 5,0        | Sistema operativo e versione. |
| >= 5,0        | ID Runtime (RID) in cui è in esecuzione lo strumento. |
| >= 5,0        | Indica se lo strumento è in esecuzione in un contenitore. |
| >= 5,0        | Indirizzo MAC (Media Access Control) con hash: un ID univoco e con hash crittografico (SHA256) per un computer. |
| >= 5,0        | Versione del kernel. |
| >= 5,0        | Versione di HttpRepl. |
| >= 5,0        | Indica se lo strumento è stato avviato con `help` `run` argomenti, o `connect` . I valori degli argomenti effettivi non vengono raccolti. |
| >= 5,0        | Comando richiamato (ad esempio, `get` ) e se ha avuto esito positivo. |
| >= 5,0        | Per il `connect` comando, se `root` `base` `openapi` sono stati specificati gli argomenti, o. I valori degli argomenti effettivi non vengono raccolti. |
| >= 5,0        | Per il `pref` comando, se `get` `set` è stato emesso un oggetto o e a quale preferenza è stato effettuato l'accesso. Se non è una preferenza nota, viene eseguito l'hashing del nome. Il valore non viene raccolto. |
| >= 5,0        | Per il `set header` comando, viene impostato il nome dell'intestazione. Se non è un'intestazione nota, viene eseguito l'hashing del nome. Il valore non viene raccolto. |
| >= 5,0        | Per il `connect` comando, se è stato usato un caso speciale per `dotnet new webapi` e, se è stato ignorato tramite preferenza. |
| >= 5,0        | Per tutti i comandi HTTP, ad esempio GET, POST, PUT, se ognuna delle opzioni è stata specificata. I valori delle opzioni non vengono raccolti. |

## <a name="additional-resources"></a>Risorse aggiuntive

* [Telemetria di .NET Core SDK](/dotnet/core/tools/telemetry)
* [Interfaccia della riga di comando di .NET Core dati di telemetria](https://dotnet.microsoft.com/platform/telemetry)
