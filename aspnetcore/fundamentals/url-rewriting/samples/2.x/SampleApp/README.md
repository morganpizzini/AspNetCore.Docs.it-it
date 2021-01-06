# <a name="aspnet-core-url-rewriting-sample"></a><span data-ttu-id="6c7b8-101">Esempio di riscrittura URL per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c7b8-101">ASP.NET Core URL Rewriting Sample</span></span>

<span data-ttu-id="6c7b8-102">Questo esempio illustra l'uso del middleware di riscrittura URL per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6c7b8-102">This sample illustrates usage of ASP.NET Core URL Rewriting Middleware.</span></span> <span data-ttu-id="6c7b8-103">L'app illustra le opzioni di reindirizzamento e di riscrittura degli URL.</span><span class="sxs-lookup"><span data-stu-id="6c7b8-103">The app demonstrates URL redirect and URL rewriting options.</span></span>

<span data-ttu-id="6c7b8-104">Quando si esegue l'esempio, al momento dell'applicazione di una delle regole a un URL di richiesta, risposte diverse da file restituiscono l'URL riscritto o reindirizzato.</span><span class="sxs-lookup"><span data-stu-id="6c7b8-104">When running the sample, non-file responses return the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span> <span data-ttu-id="6c7b8-105">Per gli esempi in formato XML e file di testo, il middleware dei file statici genera il file dopo che l'URL della richiesta è stato riscritto dal middleware.</span><span class="sxs-lookup"><span data-stu-id="6c7b8-105">For the XML and text file examples, Static File Middleware serves the file after the request URL is rewritten by the middleware.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="6c7b8-106">Esempi</span><span class="sxs-lookup"><span data-stu-id="6c7b8-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - <span data-ttu-id="6c7b8-107">Codice di stato riuscito del comando eseguito: 302 (Trovato)</span><span class="sxs-lookup"><span data-stu-id="6c7b8-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="6c7b8-108">Esempio (reindirizzamento): **/redirect-rule/{gruppo_capture}** a **/redirected/{gruppo_capture}**</span><span class="sxs-lookup"><span data-stu-id="6c7b8-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="6c7b8-109">Codice di stato riuscito del comando eseguito: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="6c7b8-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="6c7b8-110">Esempio (riscrittura): **/rewrite-rule/{gruppo_capture_1}/{gruppo_capture_2}** in **/rewritten?var1={gruppo_capture_1}&var2={gruppo_capture_2}**</span><span class="sxs-lookup"><span data-stu-id="6c7b8-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="6c7b8-111">Codice di stato riuscito del comando eseguito: 302 (Trovato)</span><span class="sxs-lookup"><span data-stu-id="6c7b8-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="6c7b8-112">Esempio (reindirizzamento): **/apache-mod-rules-redirect/{gruppo_capture}** a **/redirected?id={gruppo_capture}**</span><span class="sxs-lookup"><span data-stu-id="6c7b8-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="6c7b8-113">Codice di stato riuscito del comando eseguito: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="6c7b8-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="6c7b8-114">Esempio (riscrittura): **/iis-rules-rewrite/{gruppo_capture}** in **/rewritten?id={gruppo_capture}**</span><span class="sxs-lookup"><span data-stu-id="6c7b8-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXmlFileRequests)`
  - <span data-ttu-id="6c7b8-115">Codice di stato riuscito del comando eseguito: 301 (Spostato permanentemente)</span><span class="sxs-lookup"><span data-stu-id="6c7b8-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="6c7b8-116">Esempio (reindirizzamento): **/file.xml** a **/xmlfiles/file.xml**</span><span class="sxs-lookup"><span data-stu-id="6c7b8-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(RewriteTextFileRequests)`
  - <span data-ttu-id="6c7b8-117">Codice di stato riuscito del comando eseguito: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="6c7b8-117">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="6c7b8-118">Esempio (riscrittura): da **/some_file.txt** a **/file.txt**</span><span class="sxs-lookup"><span data-stu-id="6c7b8-118">Example (rewrite): **/some_file.txt** to **/file.txt**</span></span>
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="6c7b8-119">Codice di stato riuscito del comando eseguito: 301 (Spostato permanentemente)</span><span class="sxs-lookup"><span data-stu-id="6c7b8-119">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="6c7b8-120">Esempio (reindirizzamento): **/image.png** a **/png-images/image.png**</span><span class="sxs-lookup"><span data-stu-id="6c7b8-120">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="6c7b8-121">Esempio (reindirizzamento): **/image.jpg** a **/jpg-images/image.jpg**</span><span class="sxs-lookup"><span data-stu-id="6c7b8-121">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="use-a-physicalfileprovider"></a><span data-ttu-id="6c7b8-122">Usare un PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="6c7b8-122">Use a PhysicalFileProvider</span></span>

<span data-ttu-id="6c7b8-123">È anche possibile ottenere un oggetto `IFileProvider` creando un `PhysicalFileProvider` da passare nei metodi `AddApacheModRewrite()` e `AddIISUrlRewrite()`:</span><span class="sxs-lookup"><span data-stu-id="6c7b8-123">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a><span data-ttu-id="6c7b8-124">Estensioni di reindirizzamento protette</span><span class="sxs-lookup"><span data-stu-id="6c7b8-124">Secure redirection extensions</span></span>

<span data-ttu-id="6c7b8-125">Questo esempio include la configurazione di `WebHostBuilder` che consente all'app di usare gli URL (`https://localhost:5001`, `https://localhost`) e un certificato di test (*testCert.pfx*) per agevolare l'esplorazione dei metodi di reindirizzamento protetti.</span><span class="sxs-lookup"><span data-stu-id="6c7b8-125">This sample includes `WebHostBuilder` configuration for the app to use URLs (`https://localhost:5001`, `https://localhost`) and a test certificate (*testCert.pfx*) to assist in exploring the secure redirect methods.</span></span> <span data-ttu-id="6c7b8-126">Se la porta 443 del server è già assegnata o in uso, l'esempio `https://localhost` non funziona &mdash; rimuovere `ListenOptions` per la porta 443 nel metodo `CreateWebHostBuilder` per il file *Program.cs* o dissociare la porta 443 del server in modo da consentirne l'uso da parte di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6c7b8-126">If the server already has port 443 assigned or in use, the `https://localhost` example doesn't work&mdash;remove the `ListenOptions` for port 443 in the `CreateWebHostBuilder` method of the *Program.cs* file or unbind port 443 on the server so that Kestrel can use the port.</span></span>

| <span data-ttu-id="6c7b8-127">Metodo</span><span class="sxs-lookup"><span data-stu-id="6c7b8-127">Method</span></span>                           | <span data-ttu-id="6c7b8-128">Codice di stato</span><span class="sxs-lookup"><span data-stu-id="6c7b8-128">Status Code</span></span> |    <span data-ttu-id="6c7b8-129">Porta</span><span class="sxs-lookup"><span data-stu-id="6c7b8-129">Port</span></span>    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     <span data-ttu-id="6c7b8-130">301</span><span class="sxs-lookup"><span data-stu-id="6c7b8-130">301</span></span>     | <span data-ttu-id="6c7b8-131">null (465)</span><span class="sxs-lookup"><span data-stu-id="6c7b8-131">null (465)</span></span> |
| `.AddRedirectToHttps()`          |     <span data-ttu-id="6c7b8-132">302</span><span class="sxs-lookup"><span data-stu-id="6c7b8-132">302</span></span>     | <span data-ttu-id="6c7b8-133">null (465)</span><span class="sxs-lookup"><span data-stu-id="6c7b8-133">null (465)</span></span> |
| `.AddRedirectToHttps(301)`       |     <span data-ttu-id="6c7b8-134">301</span><span class="sxs-lookup"><span data-stu-id="6c7b8-134">301</span></span>     | <span data-ttu-id="6c7b8-135">null (465)</span><span class="sxs-lookup"><span data-stu-id="6c7b8-135">null (465)</span></span> |
| `.AddRedirectToHttps(301, 5001)` |     <span data-ttu-id="6c7b8-136">301</span><span class="sxs-lookup"><span data-stu-id="6c7b8-136">301</span></span>     |    <span data-ttu-id="6c7b8-137">5001</span><span class="sxs-lookup"><span data-stu-id="6c7b8-137">5001</span></span>    |
