---
title: Telemetria HTTP REPL
author: scottaddie
description: Informazioni sui dati di telemetria raccolti da HTTP REPL.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 8590959e43c2dda69090acb358e740b271426a44
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502031"
---
# <a name="http-repl-telemetry"></a><span data-ttu-id="e25be-103">Telemetria HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="e25be-103">HTTP REPL telemetry</span></span>

<span data-ttu-id="e25be-104">Il [ciclo http Read-Eval-Print (REPL)](xref:web-api/http-repl) include una funzionalità di telemetria che raccoglie i dati di utilizzo.</span><span class="sxs-lookup"><span data-stu-id="e25be-104">The [HTTP Read-Eval-Print Loop (REPL)](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="e25be-105">È importante che il team di REPL HTTP comprenda come viene usato lo strumento in modo che possa essere migliorato.</span><span class="sxs-lookup"><span data-stu-id="e25be-105">It's important that the HTTP REPL team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="e25be-106">Come rifiutare esplicitamente</span><span class="sxs-lookup"><span data-stu-id="e25be-106">How to opt out</span></span>

<span data-ttu-id="e25be-107">La funzionalità di telemetria HTTP REPL è abilitata per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="e25be-107">The HTTP REPL telemetry feature is enabled by default.</span></span> <span data-ttu-id="e25be-108">Per rifiutare esplicitamente la funzionalità di telemetria, impostare la variabile di ambiente `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` su `1` o `true`.</span><span class="sxs-lookup"><span data-stu-id="e25be-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="e25be-109">Divulgazione</span><span class="sxs-lookup"><span data-stu-id="e25be-109">Disclosure</span></span>

<span data-ttu-id="e25be-110">HttpRepl Visualizza un testo simile al seguente quando si esegue lo strumento per la prima volta.</span><span class="sxs-lookup"><span data-stu-id="e25be-110">HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="e25be-111">Il testo può variare leggermente a seconda della versione dello strumento in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="e25be-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="e25be-112">Questa prima esperienza riguarda la modalità di notifica della raccolta dei dati da parte di Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e25be-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

## <a name="data-points"></a><span data-ttu-id="e25be-113">Punti dati</span><span class="sxs-lookup"><span data-stu-id="e25be-113">Data points</span></span>

<span data-ttu-id="e25be-114">La funzionalità di telemetria non:</span><span class="sxs-lookup"><span data-stu-id="e25be-114">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="e25be-115">Raccogliere dati personali, ad esempio nomi utente, indirizzi di posta elettronica o URL.</span><span class="sxs-lookup"><span data-stu-id="e25be-115">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="e25be-116">Esegue l'analisi delle richieste o delle risposte HTTP.</span><span class="sxs-lookup"><span data-stu-id="e25be-116">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="e25be-117">I dati vengono inviati in modo sicuro ai server Microsoft e conservati con accesso limitato.</span><span class="sxs-lookup"><span data-stu-id="e25be-117">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="e25be-118">La tutela della privacy è importante per Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e25be-118">Protecting your privacy is important to us.</span></span> <span data-ttu-id="e25be-119">Se si ritiene che la funzionalità di telemetria raccolga dati sensibili o che i dati siano gestiti in modo non sicuro o non appropriato, eseguire una delle azioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="e25be-119">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="e25be-120">Archiviare un problema nel repository [DotNet/httprepl](https://github.com/dotnet/httprepl/issues) .</span><span class="sxs-lookup"><span data-stu-id="e25be-120">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="e25be-121">Inviare un messaggio di posta elettronica a [dotnet@microsoft.com](mailto:dotnet@microsoft.com) per l'analisi.</span><span class="sxs-lookup"><span data-stu-id="e25be-121">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="e25be-122">La funzionalità di telemetria raccoglie i dati seguenti.</span><span class="sxs-lookup"><span data-stu-id="e25be-122">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="e25be-123">Versioni di .NET SDK</span><span class="sxs-lookup"><span data-stu-id="e25be-123">.NET SDK versions</span></span> | <span data-ttu-id="e25be-124">Dati</span><span class="sxs-lookup"><span data-stu-id="e25be-124">Data</span></span> |
|--------------|------|
| <span data-ttu-id="e25be-125">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-125">>=5.0</span></span>        | <span data-ttu-id="e25be-126">Timestamp della chiamata.</span><span class="sxs-lookup"><span data-stu-id="e25be-126">Timestamp of invocation.</span></span> |
| <span data-ttu-id="e25be-127">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-127">>=5.0</span></span>        | <span data-ttu-id="e25be-128">Indirizzo IP a tre ottetti usato per determinare la posizione geografica.</span><span class="sxs-lookup"><span data-stu-id="e25be-128">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="e25be-129">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-129">>=5.0</span></span>        | <span data-ttu-id="e25be-130">Sistema operativo e versione.</span><span class="sxs-lookup"><span data-stu-id="e25be-130">Operating system and version.</span></span> |
| <span data-ttu-id="e25be-131">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-131">>=5.0</span></span>        | <span data-ttu-id="e25be-132">ID Runtime (RID) in cui è in esecuzione lo strumento.</span><span class="sxs-lookup"><span data-stu-id="e25be-132">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="e25be-133">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-133">>=5.0</span></span>        | <span data-ttu-id="e25be-134">Indica se lo strumento è in esecuzione in un contenitore.</span><span class="sxs-lookup"><span data-stu-id="e25be-134">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="e25be-135">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-135">>=5.0</span></span>        | <span data-ttu-id="e25be-136">Indirizzo MAC (Media Access Control) con hash: un ID univoco e con hash crittografico (SHA256) per un computer.</span><span class="sxs-lookup"><span data-stu-id="e25be-136">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="e25be-137">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-137">>=5.0</span></span>        | <span data-ttu-id="e25be-138">Versione del kernel.</span><span class="sxs-lookup"><span data-stu-id="e25be-138">Kernel version.</span></span> |
| <span data-ttu-id="e25be-139">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-139">>=5.0</span></span>        | <span data-ttu-id="e25be-140">Versione HTTP REPL.</span><span class="sxs-lookup"><span data-stu-id="e25be-140">HTTP REPL version.</span></span> |
| <span data-ttu-id="e25be-141">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-141">>=5.0</span></span>        | <span data-ttu-id="e25be-142">Indica se lo strumento è stato avviato con `help` `run` argomenti, o `connect` .</span><span class="sxs-lookup"><span data-stu-id="e25be-142">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="e25be-143">I valori degli argomenti effettivi non vengono raccolti.</span><span class="sxs-lookup"><span data-stu-id="e25be-143">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="e25be-144">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-144">>=5.0</span></span>        | <span data-ttu-id="e25be-145">Comando richiamato (ad esempio, `get` ) e se ha avuto esito positivo.</span><span class="sxs-lookup"><span data-stu-id="e25be-145">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="e25be-146">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-146">>=5.0</span></span>        | <span data-ttu-id="e25be-147">Per il `connect` comando, se `root` `base` `openapi` sono stati specificati gli argomenti, o.</span><span class="sxs-lookup"><span data-stu-id="e25be-147">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="e25be-148">I valori degli argomenti effettivi non vengono raccolti.</span><span class="sxs-lookup"><span data-stu-id="e25be-148">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="e25be-149">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-149">>=5.0</span></span>        | <span data-ttu-id="e25be-150">Per il `pref` comando, se `get` `set` è stato emesso un oggetto o e a quale preferenza è stato effettuato l'accesso.</span><span class="sxs-lookup"><span data-stu-id="e25be-150">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="e25be-151">Se non è una preferenza nota, viene eseguito l'hashing del nome.</span><span class="sxs-lookup"><span data-stu-id="e25be-151">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="e25be-152">Il valore non viene raccolto.</span><span class="sxs-lookup"><span data-stu-id="e25be-152">The value isn't collected.</span></span> |
| <span data-ttu-id="e25be-153">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-153">>=5.0</span></span>        | <span data-ttu-id="e25be-154">Per il `set header` comando, viene impostato il nome dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="e25be-154">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="e25be-155">Se non è un'intestazione nota, viene eseguito l'hashing del nome.</span><span class="sxs-lookup"><span data-stu-id="e25be-155">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="e25be-156">Il valore non viene raccolto.</span><span class="sxs-lookup"><span data-stu-id="e25be-156">The value isn't collected.</span></span> |
| <span data-ttu-id="e25be-157">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-157">>=5.0</span></span>        | <span data-ttu-id="e25be-158">Per il `connect` comando, se è stato usato un caso speciale per `dotnet new webapi` e, se è stato ignorato tramite preferenza.</span><span class="sxs-lookup"><span data-stu-id="e25be-158">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="e25be-159">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="e25be-159">>=5.0</span></span>        | <span data-ttu-id="e25be-160">Per tutti i comandi HTTP, ad esempio GET, POST, PUT, se ognuna delle opzioni è stata specificata.</span><span class="sxs-lookup"><span data-stu-id="e25be-160">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="e25be-161">I valori delle opzioni non vengono raccolti.</span><span class="sxs-lookup"><span data-stu-id="e25be-161">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="e25be-162">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e25be-162">Additional resources</span></span>

* [<span data-ttu-id="e25be-163">Telemetria di .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="e25be-163">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="e25be-164">Interfaccia della riga di comando di .NET Core dati di telemetria</span><span class="sxs-lookup"><span data-stu-id="e25be-164">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
