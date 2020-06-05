---
title: Testare le API Web con il ciclo Read-Eval-Print (REPL) HTTP
author: scottaddie
description: Informazioni su come usare lo strumento globale REPL HTTP .NET Core per esplorare e testare un'API Web ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/http-repl
ms.openlocfilehash: 4c42ad56bbdb7b66824b290cd118903cbe4311e8
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452213"
---
# <a name="test-web-apis-with-the-http-repl"></a>Testare le API Web con il ciclo Read-Eval-Print (REPL) HTTP

Di [Scott Addie](https://twitter.com/Scott_Addie)

Il ciclo Read-Eval-Print (REPL) HTTP:

* È un strumento da riga di comando multipiattaforma leggero supportato ovunque sia supportato .NET Core.
* Consente di eseguire richieste HTTP per testare le API Web ASP.NET Core (e le API Web non ASP.NET Core) e visualizzare i relativi risultati.
* Riesce a testare le API Web ospitate in qualsiasi ambiente, inclusi localhost e Servizio app di Azure.

Sono supportati i [verbi HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) seguenti:

* [DELETE](#test-http-delete-requests)
* [GET](#test-http-get-requests)
* [HEAD](#test-http-head-requests)
* [Opzioni](#test-http-options-requests)
* [PATCH](#test-http-patch-requests)
* [POST](#test-http-post-requests)
* [METTERE](#test-http-put-requests)

Per continuare, [visualizzare o scaricare l'API Web ASP.NET Core di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([come scaricare](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Prerequisiti

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a>Installazione

Per installare il ciclo Read-Eval-Print HTTP, eseguire il comando seguente:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

Viene installato uno [strumento globale .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) dal pacchetto NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).

## <a name="usage"></a>Utilizzo

Dopo aver completato l'installazione dello strumento, eseguire il comando seguente per avviare il ciclo Read-Eval-Print HTTP:

```console
httprepl
```

Per visualizzare i comandi del ciclo Read-Eval-Print HTTP disponibili, eseguire uno dei comandi seguenti:

```console
httprepl -h
```

```console
httprepl --help
```

Verrà visualizzato l'output seguente:

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

Il ciclo Read-Eval-Print HTTP offre il completamento del comando. Premendo il tasto <kbd>TAB</kbd> è possibile scorrere l'elenco dei comandi che completano i caratteri o l'endpoint API digitato. Le sezioni seguenti descrivono i comandi dell'interfaccia della riga di comando disponibili.

## <a name="connect-to-the-web-api"></a>Connettersi all'API Web

Connettersi all'API Web eseguendo il comando seguente:

```console
httprepl <ROOT URI>
```

`<ROOT URI>` è l'URI di base dell'API Web. Ad esempio:

```console
httprepl https://localhost:5001
```

In alternativa, eseguire il comando seguente in qualsiasi momento mentre è in esecuzione il ciclo Read-Eval-Print HTTP:

```console
connect <ROOT URI>
```

Ad esempio:

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a>Puntare manualmente al documento di Swagger per l'API Web

Il comando connect precedente tenterà di individuare automaticamente il documento di Swagger. Se per qualche motivo l'individuazione non riesce, è possibile specificare l'URI del documento di Swagger per l'API Web usando l'opzione `--swagger`:

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

Ad esempio:

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a>Esplorare l'API Web

### <a name="view-available-endpoints"></a>Visualizzare gli endpoint disponibili

Per visualizzare l'elenco dei diversi endpoint (controller) nel percorso corrente dell'indirizzo dell'API Web, eseguire il comando `ls` o `dir`:

```console
https://localhot:5001/~ ls
```

Viene visualizzato il formato di output seguente:

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

L'output precedente indica che sono disponibili due controller: `Fruits` e `People`. Entrambi i controller supportano operazioni HTTP GET e POST senza parametri.

L'esplorazione di un controller specifico offre altri dettagli. Ad esempio, l'output del comando seguente mostra che il controller `Fruits` supporta anche le operazioni HTTP GET, PUT e DELETE. Ognuna di queste operazioni prevede un parametro `id` nella route:

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

In alternativa, eseguire il comando `ui` per aprire la pagina dell'interfaccia utente di Swagger dell'API Web in un browser. Ad esempio:

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a>Passare a un endpoint

Per passare a un endpoint diverso nell'API Web, eseguire il comando `cd`:

```console
https://localhost:5001/~ cd people
```

Nel percorso che segue il comando `cd` non viene fatta distinzione tra maiuscole e minuscole. Viene visualizzato il formato di output seguente:

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a>Personalizzare il ciclo Read-Eval-Print HTTP

I [colori](#set-color-preferences) predefiniti del ciclo Read-Eval-Print HTTP possono essere personalizzati. È anche possibile definire un [editor di testo predefinito](#set-the-default-text-editor). Le preferenze del ciclo Read-Eval-Print HTTP vengono mantenute nella sessione corrente e nelle sessioni future. Dopo essere state modificate, le preferenze vengono archiviate nel file seguente:

# <a name="linux"></a>[Linux](#tab/linux)

*%HOME%/.httpreplprefs*

# <a name="macos"></a>[macOS](#tab/macos)

*%HOME%/.httpreplprefs*

# <a name="windows"></a>[Windows](#tab/windows)

*%USERPROFILE%\\.httpreplprefs*

---

Il file con estensione *httpreplprefs* viene caricato all'avvio e non viene monitorato per le modifiche in fase di esecuzione. Le modifiche manuali apportate al file diventano effettive solo dopo il riavvio dello strumento.

### <a name="view-the-settings"></a>Visualizzare le impostazioni

Per visualizzare le impostazioni disponibili, eseguire il comando `pref get`. Ad esempio:

```console
https://localhost:5001/~ pref get
```

Il comando precedente visualizza le coppie chiave-valore disponibili:

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a>Impostare le preferenze colori

La colorazione delle risposte è attualmente supportata solo per JSON. Per personalizzare la colorazione predefinita dello strumento del ciclo Read-Eval-Print, individuare la chiave corrispondente al colore da modificare. Per istruzioni su come trovare le chiavi, vedere la sezione [Visualizzare le impostazioni](#view-the-settings). Ad esempio, modificare il valore della chiave `colors.json` da `Green` a `White` come indicato di seguito:

```console
https://localhost:5001/people~ pref set colors.json White
```

È possibile usare solo i [colori consentiti](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs). Output delle richieste HTTP successive con la nuova colorazione.

Quando non sono impostate chiavi di colore specifiche, vengono considerate chiavi più generiche. Per comprendere questo comportamento di fallback, si consideri l'esempio seguente:

* Se `colors.json.name` non ha un valore, viene usato `colors.json.string`.
* Se `colors.json.string` non ha un valore, viene usato `colors.json.literal`.
* Se `colors.json.literal` non ha un valore, viene usato `colors.json`. 
* Se `colors.json` non ha un valore, viene usato il colore del testo predefinito della shell dei comandi (`AllowedColors.None`).

### <a name="set-indentation-size"></a>Impostare la dimensione del rientro

La personalizzazione della dimensione del rientro delle risposte è attualmente supportata solo per JSON. La dimensione del rientro predefinita è di due spazi. Ad esempio:

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

Per modificare la dimensione predefinita, impostare la chiave `formatting.json.indentSize`. Ad esempio, per usare sempre quattro spazi:

```console
pref set formatting.json.indentSize 4
```

L'impostazione dei quattro spazi viene applicata alle risposte successive:

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a>Impostare l'editor di testo predefinito

Per impostazione predefinita, il ciclo Read-Eval-Print HTTP non ha un editor di testo configurato per l'utilizzo. Per testare i metodi dell'API Web che richiedono un corpo della richiesta HTTP, è necessario impostare un editor di testo predefinito. Lo strumento del ciclo Read-Eval-Print HTTP avvia l'editor di testo configurato al solo scopo di comporre il corpo della richiesta. Eseguire il comando seguente per impostare l'editor di testo preferito come predefinito:

```console
pref set editor.command.default "<EXECUTABLE>"
```

Nel comando precedente `<EXECUTABLE>` è il percorso completo del file eseguibile dell'editor di testo. Ad esempio, eseguire il comando seguente per impostare Visual Studio Code come editor di testo predefinito:

# <a name="linux"></a>[Linux](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[macOS](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[Windows](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

Per avviare l'editor di testo predefinito con argomenti dell'interfaccia della riga di comando specifici, impostare la chiave `editor.command.default.arguments`. Si supponga, ad esempio, che Visual Studio Code sia l'editor di testo predefinito e che si voglia che il ciclo Read-Eval-Print apra sempre Visual Studio Code in una nuova sessione con le estensioni disabilitate. Eseguire il comando seguente:

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a>Impostare i percorsi di ricerca di Swagger

Per impostazione predefinita, HTTP REPL ha un set di percorsi relativi che usa per trovare il documento di Swagger quando si esegue il comando `connect` senza l'opzione `--swagger`. Questi percorsi relativi vengono combinati con i percorsi radice e di base specificati nel comando `connect`. I percorsi relativi predefiniti sono i seguenti:

- *spavalderia. JSON*
- *spavalderia/V1/spavalderia. JSON*
- */Swagger.JSON*
- */swagger/v1/swagger.json*

Per usare un set di percorsi di ricerca diverso nell'ambiente in uso, impostare la preferenza `swagger.searchPaths`. Il valore deve essere un elenco di percorsi relativi delimitati da pipe. Ad esempio:

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a>Testare le richieste HTTP GET

### <a name="synopsis"></a>Riepilogo

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argomenti

`PARAMETER`

Parametro di route, se presente, previsto dal metodo di azione del controller associato.

### <a name="options"></a>Opzioni

Per il comando `get` sono disponibili le opzioni seguenti:

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Esempio

Per inviare una richiesta HTTP GET:

1. Eseguire il comando `get` in un endpoint che lo supporta:

    ```console
    https://localhost:5001/people~ get
    ```

    Il comando precedente visualizza il formato di output seguente:

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. Recuperare un record specifico passando un parametro al comando `get`:

    ```console
    https://localhost:5001/people~ get 2
    ```

    Il comando precedente visualizza il formato di output seguente:

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a>Testare le richieste HTTP POST

### <a name="synopsis"></a>Riepilogo

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argomenti

`PARAMETER`

Parametro di route, se presente, previsto dal metodo di azione del controller associato.

### <a name="options"></a>Opzioni

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Esempio

Per inviare una richiesta HTTP POST:

1. Eseguire il comando `post` in un endpoint che lo supporta:

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    Nel comando precedente l'intestazione della richiesta HTTP `Content-Type` è impostata per indicare un tipo di supporto del corpo della richiesta JSON. L'editor di testo predefinito apre un file con estensione *tmp* con un modello JSON che rappresenta il corpo della richiesta HTTP. Ad esempio:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Per impostare l'editor di testo predefinito, vedere la sezione [Impostare l'editor di testo predefinito](#set-the-default-text-editor).

1. Modificare il modello JSON per soddisfare i requisiti di convalida del modello:

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. Salvare il file con estensione *tmp* e chiudere l'editor di testo. Nella shell dei comandi viene visualizzato l'output seguente:

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a>Testare le richieste HTTP PUT

### <a name="synopsis"></a>Riepilogo

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argomenti

`PARAMETER`

Parametro di route, se presente, previsto dal metodo di azione del controller associato.

### <a name="options"></a>Opzioni

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Esempio

Per inviare una richiesta HTTP PUT:

1. *Facoltativo*: eseguire il `get` comando per visualizzare i dati prima di modificarli:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. Eseguire il comando `put` in un endpoint che lo supporta:

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    Nel comando precedente l'intestazione della richiesta HTTP `Content-Type` è impostata per indicare un tipo di supporto del corpo della richiesta JSON. L'editor di testo predefinito apre un file con estensione *tmp* con un modello JSON che rappresenta il corpo della richiesta HTTP. Ad esempio:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Per impostare l'editor di testo predefinito, vedere la sezione [Impostare l'editor di testo predefinito](#set-the-default-text-editor).

1. Modificare il modello JSON per soddisfare i requisiti di convalida del modello:

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. Salvare il file con estensione *tmp* e chiudere l'editor di testo. Nella shell dei comandi viene visualizzato l'output seguente:

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. *Facoltativo*: eseguire un `get` comando per visualizzare le modifiche. Ad esempio, se si digita "Cherry" nell'editor di testo, `get` restituisce quanto segue:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a>Testare le richieste HTTP DELETE

### <a name="synopsis"></a>Riepilogo

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argomenti

`PARAMETER`

Parametro di route, se presente, previsto dal metodo di azione del controller associato.

### <a name="options"></a>Opzioni

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Esempio

Per inviare una richiesta HTTP DELETE:

1. *Facoltativo*: eseguire il `get` comando per visualizzare i dati prima di modificarli:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. Eseguire il comando `delete` in un endpoint che lo supporta:

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    Il comando precedente visualizza il formato di output seguente:

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. *Facoltativo*: eseguire un `get` comando per visualizzare le modifiche. In questo esempio `get` restituisce quanto segue:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a>Testare le richieste HTTP PATCH

### <a name="synopsis"></a>Riepilogo

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argomenti

`PARAMETER`

Parametro di route, se presente, previsto dal metodo di azione del controller associato.

### <a name="options"></a>Opzioni

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a>Testare le richieste HTTP HEAD

### <a name="synopsis"></a>Riepilogo

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argomenti

`PARAMETER`

Parametro di route, se presente, previsto dal metodo di azione del controller associato.

### <a name="options"></a>Opzioni

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a>Testare le richieste HTTP OPTIONS

### <a name="synopsis"></a>Riepilogo

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argomenti

`PARAMETER`

Parametro di route, se presente, previsto dal metodo di azione del controller associato.

### <a name="options"></a>Opzioni

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a>Impostare le intestazioni delle richieste HTTP

Per impostare l'intestazione di una richiesta HTTP, usare uno degli approcci seguenti:

* Impostare inline con la richiesta HTTP. Ad esempio:

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```
    
    Con l'approccio precedente, ogni singola intestazione di richiesta HTTP richiede una propria opzione `-h`.

* Impostare prima di inviare la richiesta HTTP. Ad esempio:

    ```console
    https://localhost:5001/people~ set header Content-Type application/json
    ```
    
    Quando si imposta l'intestazione prima di inviare una richiesta, l'intestazione rimane impostata per la durata della sessione della shell dei comandi. Per cancellare l'intestazione, specificare un valore vuoto. Ad esempio:
    
    ```console
    https://localhost:5001/people~ set header Content-Type
    ```

## <a name="test-secured-endpoints"></a>Testare endpoint protetti

Il REPL HTTP supporta il test degli endpoint protetti in due modi: tramite le credenziali predefinite dell'utente connesso o tramite l'utilizzo di intestazioni di richiesta HTTP. 

### <a name="default-credentials"></a>Credenziali predefinite

Si consideri uno scenario in cui l'API Web che si sta testando è ospitata in IIS ed è protetta con l'autenticazione di Windows. Si desidera che le credenziali dell'utente che esegue lo strumento scorrano tra gli endpoint HTTP sottoposti a test. Per passare le credenziali predefinite dell'utente connesso:

1. Impostare la `httpClient.useDefaultCredentials` preferenza su `true` :

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. Chiudere e riavviare lo strumento prima di inviare un'altra richiesta all'API Web.

### <a name="http-request-headers"></a>Intestazioni di richiesta HTTP

Esempi di schemi di autenticazione e autorizzazione supportati includono l'autenticazione di base, i token di porta JWT e l'autenticazione del digest. Ad esempio, è possibile inviare un bearer token a un endpoint con il comando seguente:

```console
set header Authorization "bearer <TOKEN VALUE>"
```

Per accedere a un endpoint ospitato da Azure o per usare l' [API REST di Azure](/rest/api/azure/), è necessario un Bearer token. Usare la procedura seguente per ottenere un bearer token per la sottoscrizione di Azure tramite l'interfaccia della riga di comando di [Azure](/cli/azure/). Il REPL HTTP imposta la bearer token in un'intestazione di richiesta HTTP e recupera un elenco di app Azure app Web del servizio.

1. Accedere ad Azure:

    ```azurecli
    az login
    ```

1. Ottenere l'ID sottoscrizione con il comando seguente:

    ```azurecli
    az account show --query id
    ```

1. Copiare l'ID sottoscrizione ed eseguire il comando seguente:

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. Ottenere il bearer token con il comando seguente:

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. Connettersi all'API REST di Azure tramite il REPL HTTP:

    ```console
    httprepl https://management.azure.com
    ```

1. Impostare l' `Authorization` intestazione della richiesta http:

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. Passare alla sottoscrizione:

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. Ottenere un elenco delle app Web del servizio app Azure della sottoscrizione:

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    Viene visualizzata la risposta seguente:

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a>Attivare o disattivare la visualizzazione delle richieste HTTP

Per impostazione predefinita, la visualizzazione della richiesta HTTP inviata viene eliminata. È possibile modificare l'impostazione corrispondente per la durata della sessione della shell dei comandi.

### <a name="enable-request-display"></a>Abilitare la visualizzazione delle richieste

Visualizzare la richiesta HTTP inviata eseguendo il comando `echo on`. Ad esempio:

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

Le richieste HTTP successive della sessione corrente visualizzano le intestazioni delle richieste. Ad esempio:

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a>Disabilitare la visualizzazione delle richieste

Eliminare la visualizzazione della richiesta HTTP inviata eseguendo il comando `echo off`. Ad esempio:

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a>Esegui uno script

Se si esegue spesso lo stesso set di comandi del ciclo Read-Eval-Print HTTP, può essere utile archiviarlo in un file di testo. I comandi nel file hanno lo stesso formato di quelli eseguiti manualmente nella riga di comando. I comandi possono essere eseguiti in modalità batch usando il comando `run`. Ad esempio:

1. Creare un file di testo contenente un set di comandi delimitati da una nuova riga. Si consideri ad esempio un file *people-script.txt* contenente i comandi seguenti:

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. Eseguire il comando `run` passando il percorso del file di testo. Ad esempio:

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    Viene visualizzato l'output seguente:

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a>Cancellare l'output

Per rimuovere l'intero output scritto nella shell dei comandi dallo strumento del ciclo Read-Eval-Print HTTP, eseguire il comando `clear` o `cls`. Si supponga ad esempio che la shell dei comandi includa l'output seguente:

```console
httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

Eseguire il comando seguente per cancellare l'output:

```console
https://localhost:5001/~ clear
```

Dopo aver eseguito il comando precedente, la shell dei comandi contiene solo l'output seguente:

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a>Risorse aggiuntive

* [Richieste API REST](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [Repository GitHub del ciclo Read-Eval-Print HTTP](https://github.com/dotnet/HttpRepl)
