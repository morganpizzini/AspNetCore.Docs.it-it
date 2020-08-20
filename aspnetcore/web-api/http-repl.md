---
title: Testare le API Web con il ciclo Read-Eval-Print (REPL) HTTP
author: scottaddie
description: Informazioni su come usare lo strumento globale REPL HTTP .NET Core per esplorare e testare un'API Web ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
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
uid: web-api/http-repl
ms.openlocfilehash: e6263f19cdb7f9957fa8360f9e782e622589ea18
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633318"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="9898e-103">Testare le API Web con il ciclo Read-Eval-Print (REPL) HTTP</span><span class="sxs-lookup"><span data-stu-id="9898e-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="9898e-104">Di [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="9898e-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="9898e-105">Il ciclo Read-Eval-Print (REPL) HTTP:</span><span class="sxs-lookup"><span data-stu-id="9898e-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="9898e-106">È un strumento da riga di comando multipiattaforma leggero supportato ovunque sia supportato .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9898e-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="9898e-107">Consente di eseguire richieste HTTP per testare le API Web ASP.NET Core (e le API Web non ASP.NET Core) e visualizzare i relativi risultati.</span><span class="sxs-lookup"><span data-stu-id="9898e-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="9898e-108">Riesce a testare le API Web ospitate in qualsiasi ambiente, inclusi localhost e Servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="9898e-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="9898e-109">Sono supportati i [verbi HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) seguenti:</span><span class="sxs-lookup"><span data-stu-id="9898e-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="9898e-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="9898e-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="9898e-111">GET</span><span class="sxs-lookup"><span data-stu-id="9898e-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="9898e-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="9898e-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="9898e-113">Opzioni</span><span class="sxs-lookup"><span data-stu-id="9898e-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="9898e-114">PATCH</span><span class="sxs-lookup"><span data-stu-id="9898e-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="9898e-115">POST</span><span class="sxs-lookup"><span data-stu-id="9898e-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="9898e-116">PUT</span><span class="sxs-lookup"><span data-stu-id="9898e-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="9898e-117">Per continuare, [visualizzare o scaricare l'API Web ASP.NET Core di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([come scaricare](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9898e-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9898e-118">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="9898e-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="9898e-119">Installazione</span><span class="sxs-lookup"><span data-stu-id="9898e-119">Installation</span></span>

<span data-ttu-id="9898e-120">Per installare il ciclo Read-Eval-Print HTTP, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-120">To install the HTTP REPL, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="9898e-121">Viene installato uno [strumento globale .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) dal pacchetto NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).</span><span class="sxs-lookup"><span data-stu-id="9898e-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="9898e-122">Uso</span><span class="sxs-lookup"><span data-stu-id="9898e-122">Usage</span></span>

<span data-ttu-id="9898e-123">Dopo aver completato l'installazione dello strumento, eseguire il comando seguente per avviare il ciclo Read-Eval-Print HTTP:</span><span class="sxs-lookup"><span data-stu-id="9898e-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
httprepl
```

<span data-ttu-id="9898e-124">Per visualizzare i comandi del ciclo Read-Eval-Print HTTP disponibili, eseguire uno dei comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9898e-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="9898e-125">Verrà visualizzato l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-125">The following output is displayed:</span></span>

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

<span data-ttu-id="9898e-126">Il ciclo Read-Eval-Print HTTP offre il completamento del comando.</span><span class="sxs-lookup"><span data-stu-id="9898e-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="9898e-127">Premendo il tasto <kbd>TAB</kbd> è possibile scorrere l'elenco dei comandi che completano i caratteri o l'endpoint API digitato.</span><span class="sxs-lookup"><span data-stu-id="9898e-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="9898e-128">Le sezioni seguenti descrivono i comandi dell'interfaccia della riga di comando disponibili.</span><span class="sxs-lookup"><span data-stu-id="9898e-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="9898e-129">Connettersi all'API Web</span><span class="sxs-lookup"><span data-stu-id="9898e-129">Connect to the web API</span></span>

<span data-ttu-id="9898e-130">Connettersi all'API Web eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="9898e-131">`<ROOT URI>` è l'URI di base dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="9898e-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="9898e-132">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="9898e-133">In alternativa, eseguire il comando seguente in qualsiasi momento mentre è in esecuzione il ciclo Read-Eval-Print HTTP:</span><span class="sxs-lookup"><span data-stu-id="9898e-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="9898e-134">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-134">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="9898e-135">Puntare manualmente al documento di Swagger per l'API Web</span><span class="sxs-lookup"><span data-stu-id="9898e-135">Manually point to the Swagger document for the web API</span></span>

<span data-ttu-id="9898e-136">Il comando connect precedente tenterà di individuare automaticamente il documento di Swagger.</span><span class="sxs-lookup"><span data-stu-id="9898e-136">The connect command above will attempt to find the Swagger document automatically.</span></span> <span data-ttu-id="9898e-137">Se per qualche motivo l'individuazione non riesce, è possibile specificare l'URI del documento di Swagger per l'API Web usando l'opzione `--swagger`:</span><span class="sxs-lookup"><span data-stu-id="9898e-137">If for some reason it is unable to do so, you can specify the URI of the Swagger document for the web API by using the `--swagger` option:</span></span>

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

<span data-ttu-id="9898e-138">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-138">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="9898e-139">Esplorare l'API Web</span><span class="sxs-lookup"><span data-stu-id="9898e-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="9898e-140">Visualizzare gli endpoint disponibili</span><span class="sxs-lookup"><span data-stu-id="9898e-140">View available endpoints</span></span>

<span data-ttu-id="9898e-141">Per visualizzare l'elenco dei diversi endpoint (controller) nel percorso corrente dell'indirizzo dell'API Web, eseguire il comando `ls` o `dir`:</span><span class="sxs-lookup"><span data-stu-id="9898e-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="9898e-142">Viene visualizzato il formato di output seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="9898e-143">L'output precedente indica che sono disponibili due controller: `Fruits` e `People`.</span><span class="sxs-lookup"><span data-stu-id="9898e-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="9898e-144">Entrambi i controller supportano operazioni HTTP GET e POST senza parametri.</span><span class="sxs-lookup"><span data-stu-id="9898e-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="9898e-145">L'esplorazione di un controller specifico offre altri dettagli.</span><span class="sxs-lookup"><span data-stu-id="9898e-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="9898e-146">Ad esempio, l'output del comando seguente mostra che il controller `Fruits` supporta anche le operazioni HTTP GET, PUT e DELETE.</span><span class="sxs-lookup"><span data-stu-id="9898e-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="9898e-147">Ognuna di queste operazioni prevede un parametro `id` nella route:</span><span class="sxs-lookup"><span data-stu-id="9898e-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="9898e-148">In alternativa, eseguire il comando `ui` per aprire la pagina dell'interfaccia utente di Swagger dell'API Web in un browser.</span><span class="sxs-lookup"><span data-stu-id="9898e-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="9898e-149">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="9898e-150">Passare a un endpoint</span><span class="sxs-lookup"><span data-stu-id="9898e-150">Navigate to an endpoint</span></span>

<span data-ttu-id="9898e-151">Per passare a un endpoint diverso nell'API Web, eseguire il comando `cd`:</span><span class="sxs-lookup"><span data-stu-id="9898e-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="9898e-152">Nel percorso che segue il comando `cd` non viene fatta distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="9898e-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="9898e-153">Viene visualizzato il formato di output seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="9898e-154">Personalizzare il ciclo Read-Eval-Print HTTP</span><span class="sxs-lookup"><span data-stu-id="9898e-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="9898e-155">I [colori](#set-color-preferences) predefiniti del ciclo Read-Eval-Print HTTP possono essere personalizzati.</span><span class="sxs-lookup"><span data-stu-id="9898e-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="9898e-156">È anche possibile definire un [editor di testo predefinito](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="9898e-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="9898e-157">Le preferenze del ciclo Read-Eval-Print HTTP vengono mantenute nella sessione corrente e nelle sessioni future.</span><span class="sxs-lookup"><span data-stu-id="9898e-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="9898e-158">Dopo essere state modificate, le preferenze vengono archiviate nel file seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="9898e-159">Linux</span><span class="sxs-lookup"><span data-stu-id="9898e-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="9898e-160">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="9898e-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="9898e-161">macOS</span><span class="sxs-lookup"><span data-stu-id="9898e-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="9898e-162">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="9898e-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="9898e-163">Windows</span><span class="sxs-lookup"><span data-stu-id="9898e-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="9898e-164">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="9898e-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="9898e-165">Il file con estensione *httpreplprefs* viene caricato all'avvio e non viene monitorato per le modifiche in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="9898e-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="9898e-166">Le modifiche manuali apportate al file diventano effettive solo dopo il riavvio dello strumento.</span><span class="sxs-lookup"><span data-stu-id="9898e-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="9898e-167">Visualizzare le impostazioni</span><span class="sxs-lookup"><span data-stu-id="9898e-167">View the settings</span></span>

<span data-ttu-id="9898e-168">Per visualizzare le impostazioni disponibili, eseguire il comando `pref get`.</span><span class="sxs-lookup"><span data-stu-id="9898e-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="9898e-169">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="9898e-170">Il comando precedente visualizza le coppie chiave-valore disponibili:</span><span class="sxs-lookup"><span data-stu-id="9898e-170">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="9898e-171">Impostare le preferenze colori</span><span class="sxs-lookup"><span data-stu-id="9898e-171">Set color preferences</span></span>

<span data-ttu-id="9898e-172">La colorazione delle risposte è attualmente supportata solo per JSON.</span><span class="sxs-lookup"><span data-stu-id="9898e-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="9898e-173">Per personalizzare la colorazione predefinita dello strumento del ciclo Read-Eval-Print, individuare la chiave corrispondente al colore da modificare.</span><span class="sxs-lookup"><span data-stu-id="9898e-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="9898e-174">Per istruzioni su come trovare le chiavi, vedere la sezione [Visualizzare le impostazioni](#view-the-settings).</span><span class="sxs-lookup"><span data-stu-id="9898e-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="9898e-175">Ad esempio, modificare il valore della chiave `colors.json` da `Green` a `White` come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="9898e-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="9898e-176">È possibile usare solo i [colori consentiti](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs).</span><span class="sxs-lookup"><span data-stu-id="9898e-176">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="9898e-177">Output delle richieste HTTP successive con la nuova colorazione.</span><span class="sxs-lookup"><span data-stu-id="9898e-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="9898e-178">Quando non sono impostate chiavi di colore specifiche, vengono considerate chiavi più generiche.</span><span class="sxs-lookup"><span data-stu-id="9898e-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="9898e-179">Per comprendere questo comportamento di fallback, si consideri l'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="9898e-180">Se `colors.json.name` non ha un valore, viene usato `colors.json.string`.</span><span class="sxs-lookup"><span data-stu-id="9898e-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="9898e-181">Se `colors.json.string` non ha un valore, viene usato `colors.json.literal`.</span><span class="sxs-lookup"><span data-stu-id="9898e-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="9898e-182">Se `colors.json.literal` non ha un valore, viene usato `colors.json`.</span><span class="sxs-lookup"><span data-stu-id="9898e-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="9898e-183">Se `colors.json` non ha un valore, viene usato il colore del testo predefinito della shell dei comandi (`AllowedColors.None`).</span><span class="sxs-lookup"><span data-stu-id="9898e-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="9898e-184">Impostare la dimensione del rientro</span><span class="sxs-lookup"><span data-stu-id="9898e-184">Set indentation size</span></span>

<span data-ttu-id="9898e-185">La personalizzazione della dimensione del rientro delle risposte è attualmente supportata solo per JSON.</span><span class="sxs-lookup"><span data-stu-id="9898e-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="9898e-186">La dimensione del rientro predefinita è di due spazi.</span><span class="sxs-lookup"><span data-stu-id="9898e-186">The default size is two spaces.</span></span> <span data-ttu-id="9898e-187">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-187">For example:</span></span>

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

<span data-ttu-id="9898e-188">Per modificare la dimensione predefinita, impostare la chiave `formatting.json.indentSize`.</span><span class="sxs-lookup"><span data-stu-id="9898e-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="9898e-189">Ad esempio, per usare sempre quattro spazi:</span><span class="sxs-lookup"><span data-stu-id="9898e-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="9898e-190">L'impostazione dei quattro spazi viene applicata alle risposte successive:</span><span class="sxs-lookup"><span data-stu-id="9898e-190">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="9898e-191">Impostare l'editor di testo predefinito</span><span class="sxs-lookup"><span data-stu-id="9898e-191">Set the default text editor</span></span>

<span data-ttu-id="9898e-192">Per impostazione predefinita, il ciclo Read-Eval-Print HTTP non ha un editor di testo configurato per l'utilizzo.</span><span class="sxs-lookup"><span data-stu-id="9898e-192">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="9898e-193">Per testare i metodi dell'API Web che richiedono un corpo della richiesta HTTP, è necessario impostare un editor di testo predefinito.</span><span class="sxs-lookup"><span data-stu-id="9898e-193">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="9898e-194">Lo strumento del ciclo Read-Eval-Print HTTP avvia l'editor di testo configurato al solo scopo di comporre il corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="9898e-194">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="9898e-195">Eseguire il comando seguente per impostare l'editor di testo preferito come predefinito:</span><span class="sxs-lookup"><span data-stu-id="9898e-195">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="9898e-196">Nel comando precedente `<EXECUTABLE>` è il percorso completo del file eseguibile dell'editor di testo.</span><span class="sxs-lookup"><span data-stu-id="9898e-196">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="9898e-197">Ad esempio, eseguire il comando seguente per impostare Visual Studio Code come editor di testo predefinito:</span><span class="sxs-lookup"><span data-stu-id="9898e-197">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="9898e-198">Linux</span><span class="sxs-lookup"><span data-stu-id="9898e-198">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="9898e-199">macOS</span><span class="sxs-lookup"><span data-stu-id="9898e-199">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="9898e-200">Windows</span><span class="sxs-lookup"><span data-stu-id="9898e-200">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="9898e-201">Per avviare l'editor di testo predefinito con argomenti dell'interfaccia della riga di comando specifici, impostare la chiave `editor.command.default.arguments`.</span><span class="sxs-lookup"><span data-stu-id="9898e-201">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="9898e-202">Si supponga, ad esempio, che Visual Studio Code sia l'editor di testo predefinito e che si voglia che il ciclo Read-Eval-Print apra sempre Visual Studio Code in una nuova sessione con le estensioni disabilitate.</span><span class="sxs-lookup"><span data-stu-id="9898e-202">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="9898e-203">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-203">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a><span data-ttu-id="9898e-204">Impostare i percorsi di ricerca di Swagger</span><span class="sxs-lookup"><span data-stu-id="9898e-204">Set the Swagger search paths</span></span>

<span data-ttu-id="9898e-205">Per impostazione predefinita, HTTP REPL ha un set di percorsi relativi che usa per trovare il documento di Swagger quando si esegue il comando `connect` senza l'opzione `--swagger`.</span><span class="sxs-lookup"><span data-stu-id="9898e-205">By default, the HTTP REPL has a set of relative paths that it uses to find the Swagger document when executing the `connect` command without the `--swagger` option.</span></span> <span data-ttu-id="9898e-206">Questi percorsi relativi vengono combinati con i percorsi radice e di base specificati nel comando `connect`.</span><span class="sxs-lookup"><span data-stu-id="9898e-206">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="9898e-207">I percorsi relativi predefiniti sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="9898e-207">The default relative paths are:</span></span>

- <span data-ttu-id="9898e-208">*swagger.js*</span><span class="sxs-lookup"><span data-stu-id="9898e-208">*swagger.json*</span></span>
- <span data-ttu-id="9898e-209">*spavalderia/V1/swagger.js*</span><span class="sxs-lookup"><span data-stu-id="9898e-209">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="9898e-210">*/swagger.js*</span><span class="sxs-lookup"><span data-stu-id="9898e-210">*/swagger.json*</span></span>
- <span data-ttu-id="9898e-211">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="9898e-211">*/swagger/v1/swagger.json*</span></span>

<span data-ttu-id="9898e-212">Per usare un set di percorsi di ricerca diverso nell'ambiente in uso, impostare la preferenza `swagger.searchPaths`.</span><span class="sxs-lookup"><span data-stu-id="9898e-212">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="9898e-213">Il valore deve essere un elenco di percorsi relativi delimitati da pipe.</span><span class="sxs-lookup"><span data-stu-id="9898e-213">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="9898e-214">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-214">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="9898e-215">Testare le richieste HTTP GET</span><span class="sxs-lookup"><span data-stu-id="9898e-215">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9898e-216">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="9898e-216">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9898e-217">Argomenti</span><span class="sxs-lookup"><span data-stu-id="9898e-217">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9898e-218">Parametro di route, se presente, previsto dal metodo di azione del controller associato.</span><span class="sxs-lookup"><span data-stu-id="9898e-218">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9898e-219">Opzioni</span><span class="sxs-lookup"><span data-stu-id="9898e-219">Options</span></span>

<span data-ttu-id="9898e-220">Per il comando `get` sono disponibili le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="9898e-220">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="9898e-221">Esempio</span><span class="sxs-lookup"><span data-stu-id="9898e-221">Example</span></span>

<span data-ttu-id="9898e-222">Per inviare una richiesta HTTP GET:</span><span class="sxs-lookup"><span data-stu-id="9898e-222">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="9898e-223">Eseguire il comando `get` in un endpoint che lo supporta:</span><span class="sxs-lookup"><span data-stu-id="9898e-223">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="9898e-224">Il comando precedente visualizza il formato di output seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-224">The preceding command displays the following output format:</span></span>

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

1. <span data-ttu-id="9898e-225">Recuperare un record specifico passando un parametro al comando `get`:</span><span class="sxs-lookup"><span data-stu-id="9898e-225">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="9898e-226">Il comando precedente visualizza il formato di output seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-226">The preceding command displays the following output format:</span></span>

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

## <a name="test-http-post-requests"></a><span data-ttu-id="9898e-227">Testare le richieste HTTP POST</span><span class="sxs-lookup"><span data-stu-id="9898e-227">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9898e-228">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="9898e-228">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9898e-229">Argomenti</span><span class="sxs-lookup"><span data-stu-id="9898e-229">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9898e-230">Parametro di route, se presente, previsto dal metodo di azione del controller associato.</span><span class="sxs-lookup"><span data-stu-id="9898e-230">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9898e-231">Opzioni</span><span class="sxs-lookup"><span data-stu-id="9898e-231">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="9898e-232">Esempio</span><span class="sxs-lookup"><span data-stu-id="9898e-232">Example</span></span>

<span data-ttu-id="9898e-233">Per inviare una richiesta HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="9898e-233">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="9898e-234">Eseguire il comando `post` in un endpoint che lo supporta:</span><span class="sxs-lookup"><span data-stu-id="9898e-234">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="9898e-235">Nel comando precedente l'intestazione della richiesta HTTP `Content-Type` è impostata per indicare un tipo di supporto del corpo della richiesta JSON.</span><span class="sxs-lookup"><span data-stu-id="9898e-235">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="9898e-236">L'editor di testo predefinito apre un file con estensione *tmp* con un modello JSON che rappresenta il corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9898e-236">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="9898e-237">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-237">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="9898e-238">Per impostare l'editor di testo predefinito, vedere la sezione [Impostare l'editor di testo predefinito](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="9898e-238">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="9898e-239">Modificare il modello JSON per soddisfare i requisiti di convalida del modello:</span><span class="sxs-lookup"><span data-stu-id="9898e-239">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="9898e-240">Salvare il file con estensione *tmp* e chiudere l'editor di testo.</span><span class="sxs-lookup"><span data-stu-id="9898e-240">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="9898e-241">Nella shell dei comandi viene visualizzato l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-241">The following output appears in the command shell:</span></span>

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

## <a name="test-http-put-requests"></a><span data-ttu-id="9898e-242">Testare le richieste HTTP PUT</span><span class="sxs-lookup"><span data-stu-id="9898e-242">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9898e-243">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="9898e-243">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9898e-244">Argomenti</span><span class="sxs-lookup"><span data-stu-id="9898e-244">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9898e-245">Parametro di route, se presente, previsto dal metodo di azione del controller associato.</span><span class="sxs-lookup"><span data-stu-id="9898e-245">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9898e-246">Opzioni</span><span class="sxs-lookup"><span data-stu-id="9898e-246">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="9898e-247">Esempio</span><span class="sxs-lookup"><span data-stu-id="9898e-247">Example</span></span>

<span data-ttu-id="9898e-248">Per inviare una richiesta HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="9898e-248">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="9898e-249">*Facoltativo*: eseguire il `get` comando per visualizzare i dati prima di modificarli:</span><span class="sxs-lookup"><span data-stu-id="9898e-249">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

1. <span data-ttu-id="9898e-250">Eseguire il comando `put` in un endpoint che lo supporta:</span><span class="sxs-lookup"><span data-stu-id="9898e-250">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="9898e-251">Nel comando precedente l'intestazione della richiesta HTTP `Content-Type` è impostata per indicare un tipo di supporto del corpo della richiesta JSON.</span><span class="sxs-lookup"><span data-stu-id="9898e-251">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="9898e-252">L'editor di testo predefinito apre un file con estensione *tmp* con un modello JSON che rappresenta il corpo della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9898e-252">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="9898e-253">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-253">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="9898e-254">Per impostare l'editor di testo predefinito, vedere la sezione [Impostare l'editor di testo predefinito](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="9898e-254">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="9898e-255">Modificare il modello JSON per soddisfare i requisiti di convalida del modello:</span><span class="sxs-lookup"><span data-stu-id="9898e-255">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="9898e-256">Salvare il file con estensione *tmp* e chiudere l'editor di testo.</span><span class="sxs-lookup"><span data-stu-id="9898e-256">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="9898e-257">Nella shell dei comandi viene visualizzato l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-257">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="9898e-258">*Facoltativo*: eseguire un `get` comando per visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="9898e-258">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="9898e-259">Ad esempio, se si digita "Cherry" nell'editor di testo, `get` restituisce quanto segue:</span><span class="sxs-lookup"><span data-stu-id="9898e-259">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

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

## <a name="test-http-delete-requests"></a><span data-ttu-id="9898e-260">Testare le richieste HTTP DELETE</span><span class="sxs-lookup"><span data-stu-id="9898e-260">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9898e-261">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="9898e-261">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9898e-262">Argomenti</span><span class="sxs-lookup"><span data-stu-id="9898e-262">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9898e-263">Parametro di route, se presente, previsto dal metodo di azione del controller associato.</span><span class="sxs-lookup"><span data-stu-id="9898e-263">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9898e-264">Opzioni</span><span class="sxs-lookup"><span data-stu-id="9898e-264">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="9898e-265">Esempio</span><span class="sxs-lookup"><span data-stu-id="9898e-265">Example</span></span>

<span data-ttu-id="9898e-266">Per inviare una richiesta HTTP DELETE:</span><span class="sxs-lookup"><span data-stu-id="9898e-266">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="9898e-267">*Facoltativo*: eseguire il `get` comando per visualizzare i dati prima di modificarli:</span><span class="sxs-lookup"><span data-stu-id="9898e-267">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

1. <span data-ttu-id="9898e-268">Eseguire il comando `delete` in un endpoint che lo supporta:</span><span class="sxs-lookup"><span data-stu-id="9898e-268">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="9898e-269">Il comando precedente visualizza il formato di output seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-269">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="9898e-270">*Facoltativo*: eseguire un `get` comando per visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="9898e-270">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="9898e-271">In questo esempio `get` restituisce quanto segue:</span><span class="sxs-lookup"><span data-stu-id="9898e-271">In this example, a `get` returns the following:</span></span>

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

## <a name="test-http-patch-requests"></a><span data-ttu-id="9898e-272">Testare le richieste HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="9898e-272">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9898e-273">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="9898e-273">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9898e-274">Argomenti</span><span class="sxs-lookup"><span data-stu-id="9898e-274">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9898e-275">Parametro di route, se presente, previsto dal metodo di azione del controller associato.</span><span class="sxs-lookup"><span data-stu-id="9898e-275">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9898e-276">Opzioni</span><span class="sxs-lookup"><span data-stu-id="9898e-276">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="9898e-277">Testare le richieste HTTP HEAD</span><span class="sxs-lookup"><span data-stu-id="9898e-277">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9898e-278">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="9898e-278">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9898e-279">Argomenti</span><span class="sxs-lookup"><span data-stu-id="9898e-279">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9898e-280">Parametro di route, se presente, previsto dal metodo di azione del controller associato.</span><span class="sxs-lookup"><span data-stu-id="9898e-280">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9898e-281">Opzioni</span><span class="sxs-lookup"><span data-stu-id="9898e-281">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="9898e-282">Testare le richieste HTTP OPTIONS</span><span class="sxs-lookup"><span data-stu-id="9898e-282">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9898e-283">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="9898e-283">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9898e-284">Argomenti</span><span class="sxs-lookup"><span data-stu-id="9898e-284">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9898e-285">Parametro di route, se presente, previsto dal metodo di azione del controller associato.</span><span class="sxs-lookup"><span data-stu-id="9898e-285">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9898e-286">Opzioni</span><span class="sxs-lookup"><span data-stu-id="9898e-286">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="9898e-287">Impostare le intestazioni delle richieste HTTP</span><span class="sxs-lookup"><span data-stu-id="9898e-287">Set HTTP request headers</span></span>

<span data-ttu-id="9898e-288">Per impostare l'intestazione di una richiesta HTTP, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="9898e-288">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="9898e-289">Impostare inline con la richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9898e-289">Set inline with the HTTP request.</span></span> <span data-ttu-id="9898e-290">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-290">For example:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="9898e-291">Con l'approccio precedente, ogni singola intestazione di richiesta HTTP richiede una propria opzione `-h`.</span><span class="sxs-lookup"><span data-stu-id="9898e-291">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="9898e-292">Impostare prima di inviare la richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9898e-292">Set before sending the HTTP request.</span></span> <span data-ttu-id="9898e-293">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-293">For example:</span></span>

    ```console
    https://localhost:5001/people~ set header Content-Type application/json
    ```
    
    <span data-ttu-id="9898e-294">Quando si imposta l'intestazione prima di inviare una richiesta, l'intestazione rimane impostata per la durata della sessione della shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="9898e-294">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="9898e-295">Per cancellare l'intestazione, specificare un valore vuoto.</span><span class="sxs-lookup"><span data-stu-id="9898e-295">To clear the header, provide an empty value.</span></span> <span data-ttu-id="9898e-296">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-296">For example:</span></span>
    
    ```console
    https://localhost:5001/people~ set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="9898e-297">Testare endpoint protetti</span><span class="sxs-lookup"><span data-stu-id="9898e-297">Test secured endpoints</span></span>

<span data-ttu-id="9898e-298">Il REPL HTTP supporta il test degli endpoint protetti in due modi: tramite le credenziali predefinite dell'utente connesso o tramite l'utilizzo di intestazioni di richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9898e-298">The HTTP REPL supports the testing of secured endpoints in two ways: via the default credentials of the logged in user or through the use of HTTP request headers.</span></span> 

### <a name="default-credentials"></a><span data-ttu-id="9898e-299">Credenziali predefinite</span><span class="sxs-lookup"><span data-stu-id="9898e-299">Default credentials</span></span>

<span data-ttu-id="9898e-300">Si consideri uno scenario in cui l'API Web che si sta testando è ospitata in IIS ed è protetta con l'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="9898e-300">Consider a scenario in which the web API you're testing is hosted in IIS and is secured with Windows authentication.</span></span> <span data-ttu-id="9898e-301">Si desidera che le credenziali dell'utente che esegue lo strumento scorrano tra gli endpoint HTTP sottoposti a test.</span><span class="sxs-lookup"><span data-stu-id="9898e-301">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="9898e-302">Per passare le credenziali predefinite dell'utente connesso:</span><span class="sxs-lookup"><span data-stu-id="9898e-302">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="9898e-303">Impostare la `httpClient.useDefaultCredentials` preferenza su `true` :</span><span class="sxs-lookup"><span data-stu-id="9898e-303">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="9898e-304">Chiudere e riavviare lo strumento prima di inviare un'altra richiesta all'API Web.</span><span class="sxs-lookup"><span data-stu-id="9898e-304">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="9898e-305">Intestazioni di richiesta HTTP</span><span class="sxs-lookup"><span data-stu-id="9898e-305">HTTP request headers</span></span>

<span data-ttu-id="9898e-306">Esempi di schemi di autenticazione e autorizzazione supportati includono l'autenticazione di base, i token di porta JWT e l'autenticazione del digest.</span><span class="sxs-lookup"><span data-stu-id="9898e-306">Examples of supported authentication and authorization schemes include basic authentication, JWT bearer tokens, and digest authentication.</span></span> <span data-ttu-id="9898e-307">Ad esempio, è possibile inviare un bearer token a un endpoint con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-307">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="9898e-308">Per accedere a un endpoint ospitato da Azure o per usare l' [API REST di Azure](/rest/api/azure/), è necessario un Bearer token.</span><span class="sxs-lookup"><span data-stu-id="9898e-308">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="9898e-309">Usare la procedura seguente per ottenere un bearer token per la sottoscrizione di Azure tramite l'interfaccia della riga di comando di [Azure](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="9898e-309">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="9898e-310">Il REPL HTTP imposta la bearer token in un'intestazione di richiesta HTTP e recupera un elenco di app Azure app Web del servizio.</span><span class="sxs-lookup"><span data-stu-id="9898e-310">The HTTP REPL sets the bearer token in an HTTP request header and retrieves a list of Azure App Service Web Apps.</span></span>

1. <span data-ttu-id="9898e-311">Accedere ad Azure:</span><span class="sxs-lookup"><span data-stu-id="9898e-311">Log in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="9898e-312">Ottenere l'ID sottoscrizione con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-312">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="9898e-313">Copiare l'ID sottoscrizione ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-313">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="9898e-314">Ottenere il bearer token con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-314">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="9898e-315">Connettersi all'API REST di Azure tramite il REPL HTTP:</span><span class="sxs-lookup"><span data-stu-id="9898e-315">Connect to the Azure REST API via the HTTP REPL:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="9898e-316">Impostare l' `Authorization` intestazione della richiesta http:</span><span class="sxs-lookup"><span data-stu-id="9898e-316">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="9898e-317">Passare alla sottoscrizione:</span><span class="sxs-lookup"><span data-stu-id="9898e-317">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="9898e-318">Ottenere un elenco delle app Web del servizio app Azure della sottoscrizione:</span><span class="sxs-lookup"><span data-stu-id="9898e-318">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="9898e-319">Viene visualizzata la risposta seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-319">The following response is displayed:</span></span>

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

## <a name="toggle-http-request-display"></a><span data-ttu-id="9898e-320">Attivare o disattivare la visualizzazione delle richieste HTTP</span><span class="sxs-lookup"><span data-stu-id="9898e-320">Toggle HTTP request display</span></span>

<span data-ttu-id="9898e-321">Per impostazione predefinita, la visualizzazione della richiesta HTTP inviata viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="9898e-321">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="9898e-322">È possibile modificare l'impostazione corrispondente per la durata della sessione della shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="9898e-322">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="9898e-323">Abilitare la visualizzazione delle richieste</span><span class="sxs-lookup"><span data-stu-id="9898e-323">Enable request display</span></span>

<span data-ttu-id="9898e-324">Visualizzare la richiesta HTTP inviata eseguendo il comando `echo on`.</span><span class="sxs-lookup"><span data-stu-id="9898e-324">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="9898e-325">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-325">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="9898e-326">Le richieste HTTP successive della sessione corrente visualizzano le intestazioni delle richieste.</span><span class="sxs-lookup"><span data-stu-id="9898e-326">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="9898e-327">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-327">For example:</span></span>

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

### <a name="disable-request-display"></a><span data-ttu-id="9898e-328">Disabilitare la visualizzazione delle richieste</span><span class="sxs-lookup"><span data-stu-id="9898e-328">Disable request display</span></span>

<span data-ttu-id="9898e-329">Eliminare la visualizzazione della richiesta HTTP inviata eseguendo il comando `echo off`.</span><span class="sxs-lookup"><span data-stu-id="9898e-329">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="9898e-330">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-330">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="9898e-331">Esegui uno script</span><span class="sxs-lookup"><span data-stu-id="9898e-331">Run a script</span></span>

<span data-ttu-id="9898e-332">Se si esegue spesso lo stesso set di comandi del ciclo Read-Eval-Print HTTP, può essere utile archiviarlo in un file di testo.</span><span class="sxs-lookup"><span data-stu-id="9898e-332">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="9898e-333">I comandi nel file hanno lo stesso formato di quelli eseguiti manualmente nella riga di comando.</span><span class="sxs-lookup"><span data-stu-id="9898e-333">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="9898e-334">I comandi possono essere eseguiti in modalità batch usando il comando `run`.</span><span class="sxs-lookup"><span data-stu-id="9898e-334">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="9898e-335">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-335">For example:</span></span>

1. <span data-ttu-id="9898e-336">Creare un file di testo contenente un set di comandi delimitati da una nuova riga.</span><span class="sxs-lookup"><span data-stu-id="9898e-336">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="9898e-337">Si consideri ad esempio un file *people-script.txt* contenente i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9898e-337">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="9898e-338">Eseguire il comando `run` passando il percorso del file di testo.</span><span class="sxs-lookup"><span data-stu-id="9898e-338">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="9898e-339">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9898e-339">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="9898e-340">Viene visualizzato l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-340">The following output appears:</span></span>

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

## <a name="clear-the-output"></a><span data-ttu-id="9898e-341">Cancellare l'output</span><span class="sxs-lookup"><span data-stu-id="9898e-341">Clear the output</span></span>

<span data-ttu-id="9898e-342">Per rimuovere l'intero output scritto nella shell dei comandi dallo strumento del ciclo Read-Eval-Print HTTP, eseguire il comando `clear` o `cls`.</span><span class="sxs-lookup"><span data-stu-id="9898e-342">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="9898e-343">Si supponga ad esempio che la shell dei comandi includa l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-343">To illustrate, imagine the command shell contains the following output:</span></span>

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

<span data-ttu-id="9898e-344">Eseguire il comando seguente per cancellare l'output:</span><span class="sxs-lookup"><span data-stu-id="9898e-344">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="9898e-345">Dopo aver eseguito il comando precedente, la shell dei comandi contiene solo l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="9898e-345">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="9898e-346">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9898e-346">Additional resources</span></span>

* [<span data-ttu-id="9898e-347">Richieste API REST</span><span class="sxs-lookup"><span data-stu-id="9898e-347">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="9898e-348">Repository GitHub del ciclo Read-Eval-Print HTTP</span><span class="sxs-lookup"><span data-stu-id="9898e-348">HTTP REPL GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
