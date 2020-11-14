---
title: Telemetria HttpRepl
author: scottaddie
description: Informazioni sui dati di telemetria raccolti dal HttpRepl.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550608"
---
# <a name="httprepl-telemetry"></a><span data-ttu-id="82c60-103">Telemetria HttpRepl</span><span class="sxs-lookup"><span data-stu-id="82c60-103">HttpRepl telemetry</span></span>

<span data-ttu-id="82c60-104">Il [HttpRepl](xref:web-api/http-repl) include una funzionalità di telemetria che raccoglie i dati di utilizzo.</span><span class="sxs-lookup"><span data-stu-id="82c60-104">The [HttpRepl](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="82c60-105">È importante che il team di HttpRepl comprenda come viene usato lo strumento in modo che possa essere migliorato.</span><span class="sxs-lookup"><span data-stu-id="82c60-105">It's important that the HttpRepl team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="82c60-106">Come rifiutare esplicitamente</span><span class="sxs-lookup"><span data-stu-id="82c60-106">How to opt out</span></span>

<span data-ttu-id="82c60-107">La funzionalità di telemetria HttpRepl è abilitata per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="82c60-107">The HttpRepl telemetry feature is enabled by default.</span></span> <span data-ttu-id="82c60-108">Per rifiutare esplicitamente la funzionalità di telemetria, impostare la variabile di ambiente `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` su `1` o `true`.</span><span class="sxs-lookup"><span data-stu-id="82c60-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="82c60-109">Divulgazione</span><span class="sxs-lookup"><span data-stu-id="82c60-109">Disclosure</span></span>

<span data-ttu-id="82c60-110">Il HttpRepl Visualizza un testo simile al seguente quando si esegue lo strumento per la prima volta.</span><span class="sxs-lookup"><span data-stu-id="82c60-110">The HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="82c60-111">Il testo può variare leggermente a seconda della versione dello strumento in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="82c60-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="82c60-112">Questa prima esperienza riguarda la modalità di notifica della raccolta dei dati da parte di Microsoft.</span><span class="sxs-lookup"><span data-stu-id="82c60-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

<span data-ttu-id="82c60-113">Per escludere il testo dell'esperienza "prima esecuzione", impostare la `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` variabile di ambiente su `1` o `true` .</span><span class="sxs-lookup"><span data-stu-id="82c60-113">To suppress the "first run" experience text, set the `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` environment variable to `1` or `true`.</span></span>

## <a name="data-points"></a><span data-ttu-id="82c60-114">Punti dati</span><span class="sxs-lookup"><span data-stu-id="82c60-114">Data points</span></span>

<span data-ttu-id="82c60-115">La funzionalità di telemetria non:</span><span class="sxs-lookup"><span data-stu-id="82c60-115">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="82c60-116">Raccogliere dati personali, ad esempio nomi utente, indirizzi di posta elettronica o URL.</span><span class="sxs-lookup"><span data-stu-id="82c60-116">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="82c60-117">Esegue l'analisi delle richieste o delle risposte HTTP.</span><span class="sxs-lookup"><span data-stu-id="82c60-117">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="82c60-118">I dati vengono inviati in modo sicuro ai server Microsoft e conservati con accesso limitato.</span><span class="sxs-lookup"><span data-stu-id="82c60-118">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="82c60-119">La tutela della privacy è importante per Microsoft.</span><span class="sxs-lookup"><span data-stu-id="82c60-119">Protecting your privacy is important to us.</span></span> <span data-ttu-id="82c60-120">Se si ritiene che la funzionalità di telemetria raccolga dati sensibili o che i dati siano gestiti in modo non sicuro o non appropriato, eseguire una delle azioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="82c60-120">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="82c60-121">Archiviare un problema nel repository [DotNet/httprepl](https://github.com/dotnet/httprepl/issues) .</span><span class="sxs-lookup"><span data-stu-id="82c60-121">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="82c60-122">Inviare un messaggio di posta elettronica a [dotnet@microsoft.com](mailto:dotnet@microsoft.com) per l'analisi.</span><span class="sxs-lookup"><span data-stu-id="82c60-122">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="82c60-123">La funzionalità di telemetria raccoglie i dati seguenti.</span><span class="sxs-lookup"><span data-stu-id="82c60-123">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="82c60-124">Versioni di .NET SDK</span><span class="sxs-lookup"><span data-stu-id="82c60-124">.NET SDK versions</span></span> | <span data-ttu-id="82c60-125">Dati</span><span class="sxs-lookup"><span data-stu-id="82c60-125">Data</span></span> |
|--------------|------|
| <span data-ttu-id="82c60-126">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-126">>=5.0</span></span>        | <span data-ttu-id="82c60-127">Timestamp della chiamata.</span><span class="sxs-lookup"><span data-stu-id="82c60-127">Timestamp of invocation.</span></span> |
| <span data-ttu-id="82c60-128">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-128">>=5.0</span></span>        | <span data-ttu-id="82c60-129">Indirizzo IP a tre ottetti usato per determinare la posizione geografica.</span><span class="sxs-lookup"><span data-stu-id="82c60-129">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="82c60-130">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-130">>=5.0</span></span>        | <span data-ttu-id="82c60-131">Sistema operativo e versione.</span><span class="sxs-lookup"><span data-stu-id="82c60-131">Operating system and version.</span></span> |
| <span data-ttu-id="82c60-132">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-132">>=5.0</span></span>        | <span data-ttu-id="82c60-133">ID Runtime (RID) in cui è in esecuzione lo strumento.</span><span class="sxs-lookup"><span data-stu-id="82c60-133">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="82c60-134">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-134">>=5.0</span></span>        | <span data-ttu-id="82c60-135">Indica se lo strumento è in esecuzione in un contenitore.</span><span class="sxs-lookup"><span data-stu-id="82c60-135">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="82c60-136">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-136">>=5.0</span></span>        | <span data-ttu-id="82c60-137">Indirizzo MAC (Media Access Control) con hash: un ID univoco e con hash crittografico (SHA256) per un computer.</span><span class="sxs-lookup"><span data-stu-id="82c60-137">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="82c60-138">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-138">>=5.0</span></span>        | <span data-ttu-id="82c60-139">Versione del kernel.</span><span class="sxs-lookup"><span data-stu-id="82c60-139">Kernel version.</span></span> |
| <span data-ttu-id="82c60-140">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-140">>=5.0</span></span>        | <span data-ttu-id="82c60-141">Versione di HttpRepl.</span><span class="sxs-lookup"><span data-stu-id="82c60-141">HttpRepl version.</span></span> |
| <span data-ttu-id="82c60-142">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-142">>=5.0</span></span>        | <span data-ttu-id="82c60-143">Indica se lo strumento è stato avviato con `help` `run` argomenti, o `connect` .</span><span class="sxs-lookup"><span data-stu-id="82c60-143">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="82c60-144">I valori degli argomenti effettivi non vengono raccolti.</span><span class="sxs-lookup"><span data-stu-id="82c60-144">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="82c60-145">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-145">>=5.0</span></span>        | <span data-ttu-id="82c60-146">Comando richiamato (ad esempio, `get` ) e se ha avuto esito positivo.</span><span class="sxs-lookup"><span data-stu-id="82c60-146">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="82c60-147">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-147">>=5.0</span></span>        | <span data-ttu-id="82c60-148">Per il `connect` comando, se `root` `base` `openapi` sono stati specificati gli argomenti, o.</span><span class="sxs-lookup"><span data-stu-id="82c60-148">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="82c60-149">I valori degli argomenti effettivi non vengono raccolti.</span><span class="sxs-lookup"><span data-stu-id="82c60-149">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="82c60-150">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-150">>=5.0</span></span>        | <span data-ttu-id="82c60-151">Per il `pref` comando, se `get` `set` è stato emesso un oggetto o e a quale preferenza è stato effettuato l'accesso.</span><span class="sxs-lookup"><span data-stu-id="82c60-151">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="82c60-152">Se non è una preferenza nota, viene eseguito l'hashing del nome.</span><span class="sxs-lookup"><span data-stu-id="82c60-152">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="82c60-153">Il valore non viene raccolto.</span><span class="sxs-lookup"><span data-stu-id="82c60-153">The value isn't collected.</span></span> |
| <span data-ttu-id="82c60-154">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-154">>=5.0</span></span>        | <span data-ttu-id="82c60-155">Per il `set header` comando, viene impostato il nome dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="82c60-155">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="82c60-156">Se non è un'intestazione nota, viene eseguito l'hashing del nome.</span><span class="sxs-lookup"><span data-stu-id="82c60-156">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="82c60-157">Il valore non viene raccolto.</span><span class="sxs-lookup"><span data-stu-id="82c60-157">The value isn't collected.</span></span> |
| <span data-ttu-id="82c60-158">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-158">>=5.0</span></span>        | <span data-ttu-id="82c60-159">Per il `connect` comando, se è stato usato un caso speciale per `dotnet new webapi` e, se è stato ignorato tramite preferenza.</span><span class="sxs-lookup"><span data-stu-id="82c60-159">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="82c60-160">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="82c60-160">>=5.0</span></span>        | <span data-ttu-id="82c60-161">Per tutti i comandi HTTP, ad esempio GET, POST, PUT, se ognuna delle opzioni è stata specificata.</span><span class="sxs-lookup"><span data-stu-id="82c60-161">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="82c60-162">I valori delle opzioni non vengono raccolti.</span><span class="sxs-lookup"><span data-stu-id="82c60-162">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="82c60-163">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="82c60-163">Additional resources</span></span>

* [<span data-ttu-id="82c60-164">Telemetria di .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="82c60-164">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="82c60-165">Interfaccia della riga di comando di .NET Core dati di telemetria</span><span class="sxs-lookup"><span data-stu-id="82c60-165">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
