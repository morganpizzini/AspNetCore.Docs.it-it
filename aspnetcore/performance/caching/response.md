---
<span data-ttu-id="ef260-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-102">'Blazor'</span></span>
- <span data-ttu-id="ef260-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-103">'Identity'</span></span>
- <span data-ttu-id="ef260-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-105">'Razor'</span></span>
- <span data-ttu-id="ef260-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-106">'SignalR' uid:</span></span> 

---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="ef260-107">Memorizzazione nella cache delle risposte in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef260-107">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="ef260-108">Di [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ef260-108">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ef260-109">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ef260-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ef260-110">La memorizzazione nella cache delle risposte riduce il numero di richieste effettuate da un client o da un proxy a un server Web.</span><span class="sxs-lookup"><span data-stu-id="ef260-110">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="ef260-111">La memorizzazione nella cache delle risposte riduce anche la quantità di lavoro eseguita dal server Web per generare una risposta.</span><span class="sxs-lookup"><span data-stu-id="ef260-111">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="ef260-112">La memorizzazione nella cache delle risposte è controllata dalle intestazioni che specificano come si desidera che client, proxy e middleware memorizzino le risposte.</span><span class="sxs-lookup"><span data-stu-id="ef260-112">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="ef260-113">L' [attributo ResponseCache](#responsecache-attribute) fa parte dell'impostazione delle intestazioni di memorizzazione nella cache delle risposte.</span><span class="sxs-lookup"><span data-stu-id="ef260-113">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="ef260-114">I client e i proxy intermedi devono rispettare le intestazioni per la memorizzazione nella cache delle risposte nella [specifica HTTP 1,1 Caching](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="ef260-114">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="ef260-115">Per la memorizzazione nella cache sul lato server che segue la specifica di Caching HTTP 1,1, usare [middleware di caching della risposta](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="ef260-115">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="ef260-116">Il middleware può usare le <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> proprietà per influenzare il comportamento di memorizzazione nella cache sul lato server.</span><span class="sxs-lookup"><span data-stu-id="ef260-116">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="ef260-117">Memorizzazione nella cache delle risposte basata su HTTP</span><span class="sxs-lookup"><span data-stu-id="ef260-117">HTTP-based response caching</span></span>

<span data-ttu-id="ef260-118">La [specifica di Caching HTTP 1,1](https://tools.ietf.org/html/rfc7234) descrive il comportamento delle cache Internet.</span><span class="sxs-lookup"><span data-stu-id="ef260-118">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="ef260-119">L'intestazione HTTP primaria utilizzata per la memorizzazione nella cache è [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), che viene utilizzata per specificare le *direttive*della cache.</span><span class="sxs-lookup"><span data-stu-id="ef260-119">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="ef260-120">Le direttive controllano il comportamento di memorizzazione nella cache come richieste da client a server e quando le risposte vengono riportate dai server ai client.</span><span class="sxs-lookup"><span data-stu-id="ef260-120">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="ef260-121">Le richieste e le risposte passano attraverso i server proxy e i server proxy devono essere conformi anche alla specifica HTTP 1,1 Caching.</span><span class="sxs-lookup"><span data-stu-id="ef260-121">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="ef260-122">`Cache-Control`Le direttive comuni sono illustrate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="ef260-122">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="ef260-123">Direttiva</span><span class="sxs-lookup"><span data-stu-id="ef260-123">Directive</span></span>                                                       | <span data-ttu-id="ef260-124">Azione</span><span class="sxs-lookup"><span data-stu-id="ef260-124">Action</span></span> |
| ---
<span data-ttu-id="ef260-125">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-126">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-126">'Blazor'</span></span>
- <span data-ttu-id="ef260-127">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-127">'Identity'</span></span>
- <span data-ttu-id="ef260-128">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-128">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-129">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-129">'Razor'</span></span>
- <span data-ttu-id="ef260-130">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-130">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-131">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-132">'Blazor'</span></span>
- <span data-ttu-id="ef260-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-133">'Identity'</span></span>
- <span data-ttu-id="ef260-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-135">'Razor'</span></span>
- <span data-ttu-id="ef260-136">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-137">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-138">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-138">'Blazor'</span></span>
- <span data-ttu-id="ef260-139">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-139">'Identity'</span></span>
- <span data-ttu-id="ef260-140">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-140">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-141">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-141">'Razor'</span></span>
- <span data-ttu-id="ef260-142">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-142">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-143">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-144">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-144">'Blazor'</span></span>
- <span data-ttu-id="ef260-145">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-145">'Identity'</span></span>
- <span data-ttu-id="ef260-146">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-146">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-147">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-147">'Razor'</span></span>
- <span data-ttu-id="ef260-148">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-148">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-149">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-150">'Blazor'</span></span>
- <span data-ttu-id="ef260-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-151">'Identity'</span></span>
- <span data-ttu-id="ef260-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-153">'Razor'</span></span>
- <span data-ttu-id="ef260-154">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-155">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-156">'Blazor'</span></span>
- <span data-ttu-id="ef260-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-157">'Identity'</span></span>
- <span data-ttu-id="ef260-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-159">'Razor'</span></span>
- <span data-ttu-id="ef260-160">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-161">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-162">'Blazor'</span></span>
- <span data-ttu-id="ef260-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-163">'Identity'</span></span>
- <span data-ttu-id="ef260-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-165">'Razor'</span></span>
- <span data-ttu-id="ef260-166">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-166">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-167">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-168">'Blazor'</span></span>
- <span data-ttu-id="ef260-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-169">'Identity'</span></span>
- <span data-ttu-id="ef260-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-171">'Razor'</span></span>
- <span data-ttu-id="ef260-172">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-173">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-174">'Blazor'</span></span>
- <span data-ttu-id="ef260-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-175">'Identity'</span></span>
- <span data-ttu-id="ef260-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-177">'Razor'</span></span>
- <span data-ttu-id="ef260-178">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-179">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-180">'Blazor'</span></span>
- <span data-ttu-id="ef260-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-181">'Identity'</span></span>
- <span data-ttu-id="ef260-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-183">'Razor'</span></span>
- <span data-ttu-id="ef260-184">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-185">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-186">'Blazor'</span></span>
- <span data-ttu-id="ef260-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-187">'Identity'</span></span>
- <span data-ttu-id="ef260-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-189">'Razor'</span></span>
- <span data-ttu-id="ef260-190">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-191">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-192">'Blazor'</span></span>
- <span data-ttu-id="ef260-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-193">'Identity'</span></span>
- <span data-ttu-id="ef260-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-195">'Razor'</span></span>
- <span data-ttu-id="ef260-196">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-197">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-198">'Blazor'</span></span>
- <span data-ttu-id="ef260-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-199">'Identity'</span></span>
- <span data-ttu-id="ef260-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-201">'Razor'</span></span>
- <span data-ttu-id="ef260-202">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-203">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-204">'Blazor'</span></span>
- <span data-ttu-id="ef260-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-205">'Identity'</span></span>
- <span data-ttu-id="ef260-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-207">'Razor'</span></span>
- <span data-ttu-id="ef260-208">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-209">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-210">'Blazor'</span></span>
- <span data-ttu-id="ef260-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-211">'Identity'</span></span>
- <span data-ttu-id="ef260-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-213">'Razor'</span></span>
- <span data-ttu-id="ef260-214">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-215">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-216">'Blazor'</span></span>
- <span data-ttu-id="ef260-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-217">'Identity'</span></span>
- <span data-ttu-id="ef260-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-219">'Razor'</span></span>
- <span data-ttu-id="ef260-220">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-221">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-222">'Blazor'</span></span>
- <span data-ttu-id="ef260-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-223">'Identity'</span></span>
- <span data-ttu-id="ef260-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-225">'Razor'</span></span>
- <span data-ttu-id="ef260-226">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-227">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-228">'Blazor'</span></span>
- <span data-ttu-id="ef260-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-229">'Identity'</span></span>
- <span data-ttu-id="ef260-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-231">'Razor'</span></span>
- <span data-ttu-id="ef260-232">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-233">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-234">'Blazor'</span></span>
- <span data-ttu-id="ef260-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-235">'Identity'</span></span>
- <span data-ttu-id="ef260-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-237">'Razor'</span></span>
- <span data-ttu-id="ef260-238">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-239">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-240">'Blazor'</span></span>
- <span data-ttu-id="ef260-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-241">'Identity'</span></span>
- <span data-ttu-id="ef260-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-243">'Razor'</span></span>
- <span data-ttu-id="ef260-244">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-245">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-246">'Blazor'</span></span>
- <span data-ttu-id="ef260-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-247">'Identity'</span></span>
- <span data-ttu-id="ef260-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-249">'Razor'</span></span>
- <span data-ttu-id="ef260-250">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-251">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-252">'Blazor'</span></span>
- <span data-ttu-id="ef260-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-253">'Identity'</span></span>
- <span data-ttu-id="ef260-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-255">'Razor'</span></span>
- <span data-ttu-id="ef260-256">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-257">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-258">'Blazor'</span></span>
- <span data-ttu-id="ef260-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-259">'Identity'</span></span>
- <span data-ttu-id="ef260-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-261">'Razor'</span></span>
- <span data-ttu-id="ef260-262">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-263">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-264">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-264">'Blazor'</span></span>
- <span data-ttu-id="ef260-265">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-265">'Identity'</span></span>
- <span data-ttu-id="ef260-266">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-266">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-267">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-267">'Razor'</span></span>
- <span data-ttu-id="ef260-268">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-268">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-269">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-270">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-270">'Blazor'</span></span>
- <span data-ttu-id="ef260-271">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-271">'Identity'</span></span>
- <span data-ttu-id="ef260-272">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-272">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-273">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-273">'Razor'</span></span>
- <span data-ttu-id="ef260-274">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-274">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-275">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-276">'Blazor'</span></span>
- <span data-ttu-id="ef260-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-277">'Identity'</span></span>
- <span data-ttu-id="ef260-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-279">'Razor'</span></span>
- <span data-ttu-id="ef260-280">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-281">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-282">'Blazor'</span></span>
- <span data-ttu-id="ef260-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-283">'Identity'</span></span>
- <span data-ttu-id="ef260-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-285">'Razor'</span></span>
- <span data-ttu-id="ef260-286">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-287">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-288">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-288">'Blazor'</span></span>
- <span data-ttu-id="ef260-289">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-289">'Identity'</span></span>
- <span data-ttu-id="ef260-290">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-290">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-291">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-291">'Razor'</span></span>
- <span data-ttu-id="ef260-292">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-292">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-293">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-294">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-294">'Blazor'</span></span>
- <span data-ttu-id="ef260-295">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-295">'Identity'</span></span>
- <span data-ttu-id="ef260-296">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-296">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-297">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-297">'Razor'</span></span>
- <span data-ttu-id="ef260-298">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-298">'SignalR' uid:</span></span> 

<span data-ttu-id="ef260-299">-------------------------------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-299">-------------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-300">'Blazor'</span></span>
- <span data-ttu-id="ef260-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-301">'Identity'</span></span>
- <span data-ttu-id="ef260-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-303">'Razor'</span></span>
- <span data-ttu-id="ef260-304">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-304">'SignalR' uid:</span></span> 

<span data-ttu-id="ef260-305">--- | | [pubblico](https://tools.ietf.org/html/rfc7234#section-5.2.2.5) | Una cache può archiviare la risposta.</span><span class="sxs-lookup"><span data-stu-id="ef260-305">--- | | [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | A cache may store the response.</span></span> <span data-ttu-id="ef260-306">| | [privato](https://tools.ietf.org/html/rfc7234#section-5.2.2.6) | La risposta non deve essere archiviata da una cache condivisa.</span><span class="sxs-lookup"><span data-stu-id="ef260-306">| | [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | The response must not be stored by a shared cache.</span></span> <span data-ttu-id="ef260-307">Una cache privata può archiviare e riutilizzare la risposta.</span><span class="sxs-lookup"><span data-stu-id="ef260-307">A private cache may store and reuse the response.</span></span> <span data-ttu-id="ef260-308">| | [Max-Age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1) | Il client non accetta una risposta la cui età è superiore al numero di secondi specificato.</span><span class="sxs-lookup"><span data-stu-id="ef260-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="ef260-309">Esempi: `max-age=60` (60 secondi), `max-age=2592000` (1 mese) | | [No-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)  |  **Sulle richieste**: una cache non deve usare una risposta archiviata per soddisfare la richiesta.</span><span class="sxs-lookup"><span data-stu-id="ef260-309">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="ef260-310">Il server di origine rigenera la risposta per il client e il middleware aggiorna la risposta archiviata nella cache.</span><span class="sxs-lookup"><span data-stu-id="ef260-310">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="ef260-311">**In**risposta: non è necessario usare la risposta per una richiesta successiva senza convalida nel server di origine.</span><span class="sxs-lookup"><span data-stu-id="ef260-311">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> <span data-ttu-id="ef260-312">| | [Nessun archivio](https://tools.ietf.org/html/rfc7234#section-5.2.1.5)  |  **Sulle richieste**: una cache non deve archiviare la richiesta.</span><span class="sxs-lookup"><span data-stu-id="ef260-312">| | [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="ef260-313">**Nelle risposte**: una cache non deve archiviare alcuna parte della risposta.</span><span class="sxs-lookup"><span data-stu-id="ef260-313">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="ef260-314">Nella tabella seguente sono illustrate le altre intestazioni della cache che svolgono un ruolo nella memorizzazione nella cache.</span><span class="sxs-lookup"><span data-stu-id="ef260-314">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="ef260-315">Header</span><span class="sxs-lookup"><span data-stu-id="ef260-315">Header</span></span>                                                     | <span data-ttu-id="ef260-316">Funzione</span><span class="sxs-lookup"><span data-stu-id="ef260-316">Function</span></span> |
| ---
<span data-ttu-id="ef260-317">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-318">'Blazor'</span></span>
- <span data-ttu-id="ef260-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-319">'Identity'</span></span>
- <span data-ttu-id="ef260-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-321">'Razor'</span></span>
- <span data-ttu-id="ef260-322">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-323">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-324">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-324">'Blazor'</span></span>
- <span data-ttu-id="ef260-325">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-325">'Identity'</span></span>
- <span data-ttu-id="ef260-326">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-326">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-327">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-327">'Razor'</span></span>
- <span data-ttu-id="ef260-328">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-328">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-329">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-330">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-330">'Blazor'</span></span>
- <span data-ttu-id="ef260-331">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-331">'Identity'</span></span>
- <span data-ttu-id="ef260-332">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-332">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-333">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-333">'Razor'</span></span>
- <span data-ttu-id="ef260-334">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-334">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-335">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-336">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-336">'Blazor'</span></span>
- <span data-ttu-id="ef260-337">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-337">'Identity'</span></span>
- <span data-ttu-id="ef260-338">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-338">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-339">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-339">'Razor'</span></span>
- <span data-ttu-id="ef260-340">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-340">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-341">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-342">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-342">'Blazor'</span></span>
- <span data-ttu-id="ef260-343">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-343">'Identity'</span></span>
- <span data-ttu-id="ef260-344">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-344">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-345">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-345">'Razor'</span></span>
- <span data-ttu-id="ef260-346">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-346">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-347">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-348">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-348">'Blazor'</span></span>
- <span data-ttu-id="ef260-349">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-349">'Identity'</span></span>
- <span data-ttu-id="ef260-350">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-350">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-351">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-351">'Razor'</span></span>
- <span data-ttu-id="ef260-352">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-352">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-353">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-354">'Blazor'</span></span>
- <span data-ttu-id="ef260-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-355">'Identity'</span></span>
- <span data-ttu-id="ef260-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-357">'Razor'</span></span>
- <span data-ttu-id="ef260-358">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-359">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-360">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-360">'Blazor'</span></span>
- <span data-ttu-id="ef260-361">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-361">'Identity'</span></span>
- <span data-ttu-id="ef260-362">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-362">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-363">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-363">'Razor'</span></span>
- <span data-ttu-id="ef260-364">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-364">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-365">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-366">'Blazor'</span></span>
- <span data-ttu-id="ef260-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-367">'Identity'</span></span>
- <span data-ttu-id="ef260-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-369">'Razor'</span></span>
- <span data-ttu-id="ef260-370">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-371">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-372">'Blazor'</span></span>
- <span data-ttu-id="ef260-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-373">'Identity'</span></span>
- <span data-ttu-id="ef260-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-375">'Razor'</span></span>
- <span data-ttu-id="ef260-376">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-377">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-378">'Blazor'</span></span>
- <span data-ttu-id="ef260-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-379">'Identity'</span></span>
- <span data-ttu-id="ef260-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-381">'Razor'</span></span>
- <span data-ttu-id="ef260-382">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-383">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-384">'Blazor'</span></span>
- <span data-ttu-id="ef260-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-385">'Identity'</span></span>
- <span data-ttu-id="ef260-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-387">'Razor'</span></span>
- <span data-ttu-id="ef260-388">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-389">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-390">'Blazor'</span></span>
- <span data-ttu-id="ef260-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-391">'Identity'</span></span>
- <span data-ttu-id="ef260-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-393">'Razor'</span></span>
- <span data-ttu-id="ef260-394">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-395">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-396">'Blazor'</span></span>
- <span data-ttu-id="ef260-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-397">'Identity'</span></span>
- <span data-ttu-id="ef260-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-399">'Razor'</span></span>
- <span data-ttu-id="ef260-400">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-400">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-401">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-402">'Blazor'</span></span>
- <span data-ttu-id="ef260-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-403">'Identity'</span></span>
- <span data-ttu-id="ef260-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-405">'Razor'</span></span>
- <span data-ttu-id="ef260-406">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-407">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-408">'Blazor'</span></span>
- <span data-ttu-id="ef260-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-409">'Identity'</span></span>
- <span data-ttu-id="ef260-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-411">'Razor'</span></span>
- <span data-ttu-id="ef260-412">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-413">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-414">'Blazor'</span></span>
- <span data-ttu-id="ef260-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-415">'Identity'</span></span>
- <span data-ttu-id="ef260-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-417">'Razor'</span></span>
- <span data-ttu-id="ef260-418">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-419">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-420">'Blazor'</span></span>
- <span data-ttu-id="ef260-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-421">'Identity'</span></span>
- <span data-ttu-id="ef260-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-423">'Razor'</span></span>
- <span data-ttu-id="ef260-424">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-424">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-425">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-426">'Blazor'</span></span>
- <span data-ttu-id="ef260-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-427">'Identity'</span></span>
- <span data-ttu-id="ef260-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-429">'Razor'</span></span>
- <span data-ttu-id="ef260-430">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-431">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-432">'Blazor'</span></span>
- <span data-ttu-id="ef260-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-433">'Identity'</span></span>
- <span data-ttu-id="ef260-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-435">'Razor'</span></span>
- <span data-ttu-id="ef260-436">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-436">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-437">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-438">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-438">'Blazor'</span></span>
- <span data-ttu-id="ef260-439">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-439">'Identity'</span></span>
- <span data-ttu-id="ef260-440">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-440">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-441">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-441">'Razor'</span></span>
- <span data-ttu-id="ef260-442">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-442">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-443">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-444">'Blazor'</span></span>
- <span data-ttu-id="ef260-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-445">'Identity'</span></span>
- <span data-ttu-id="ef260-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-447">'Razor'</span></span>
- <span data-ttu-id="ef260-448">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-449">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-450">'Blazor'</span></span>
- <span data-ttu-id="ef260-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-451">'Identity'</span></span>
- <span data-ttu-id="ef260-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-453">'Razor'</span></span>
- <span data-ttu-id="ef260-454">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-455">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-456">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-456">'Blazor'</span></span>
- <span data-ttu-id="ef260-457">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-457">'Identity'</span></span>
- <span data-ttu-id="ef260-458">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-458">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-459">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-459">'Razor'</span></span>
- <span data-ttu-id="ef260-460">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-460">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-461">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-462">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-462">'Blazor'</span></span>
- <span data-ttu-id="ef260-463">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-463">'Identity'</span></span>
- <span data-ttu-id="ef260-464">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-464">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-465">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-465">'Razor'</span></span>
- <span data-ttu-id="ef260-466">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-466">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-467">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-468">'Blazor'</span></span>
- <span data-ttu-id="ef260-469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-469">'Identity'</span></span>
- <span data-ttu-id="ef260-470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-470">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-471">'Razor'</span></span>
- <span data-ttu-id="ef260-472">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-473">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-474">'Blazor'</span></span>
- <span data-ttu-id="ef260-475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-475">'Identity'</span></span>
- <span data-ttu-id="ef260-476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-476">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-477">'Razor'</span></span>
- <span data-ttu-id="ef260-478">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-478">'SignalR' uid:</span></span> 

<span data-ttu-id="ef260-479">----------------------------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-479">----------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-480">'Blazor'</span></span>
- <span data-ttu-id="ef260-481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-481">'Identity'</span></span>
- <span data-ttu-id="ef260-482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-482">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-483">'Razor'</span></span>
- <span data-ttu-id="ef260-484">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-484">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-485">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-486">'Blazor'</span></span>
- <span data-ttu-id="ef260-487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-487">'Identity'</span></span>
- <span data-ttu-id="ef260-488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-488">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-489">'Razor'</span></span>
- <span data-ttu-id="ef260-490">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-490">'SignalR' uid:</span></span> 

<span data-ttu-id="ef260-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1) | Stima della quantità di tempo in secondi trascorsa dalla generazione della risposta o dalla convalida corretta nel server di origine.</span><span class="sxs-lookup"><span data-stu-id="ef260-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> <span data-ttu-id="ef260-492">| | [Scadenza](https://tools.ietf.org/html/rfc7234#section-5.3) | Tempo trascorso il quale la risposta è considerata obsoleta.</span><span class="sxs-lookup"><span data-stu-id="ef260-492">| | [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | The time after which the response is considered stale.</span></span> <span data-ttu-id="ef260-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4) | Esiste per la compatibilità con le versioni precedenti con le cache HTTP/1.0 per l'impostazione del `no-cache` comportamento.</span><span class="sxs-lookup"><span data-stu-id="ef260-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="ef260-494">Se l' `Cache-Control` intestazione è presente, l' `Pragma` intestazione viene ignorata.</span><span class="sxs-lookup"><span data-stu-id="ef260-494">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> <span data-ttu-id="ef260-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) | Specifica che una risposta memorizzata nella cache non deve essere inviata a meno che tutti i `Vary` campi di intestazione corrispondano sia nella richiesta originale della risposta memorizzata nella cache che nella nuova richiesta.</span><span class="sxs-lookup"><span data-stu-id="ef260-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="ef260-496">La memorizzazione nella cache basata su HTTP rispetta le direttive di controllo della cache delle richieste</span><span class="sxs-lookup"><span data-stu-id="ef260-496">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="ef260-497">La [specifica di Caching HTTP 1,1 per l'intestazione Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) richiede una cache per rispettare un' `Cache-Control` intestazione valida inviata dal client.</span><span class="sxs-lookup"><span data-stu-id="ef260-497">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="ef260-498">Un client può effettuare richieste con un `no-cache` valore di intestazione e forzare il server a generare una nuova risposta per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="ef260-498">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="ef260-499">Per rispettare sempre `Cache-Control` le intestazioni delle richieste client, è opportuno considerare l'obiettivo della memorizzazione nella cache HTTP.</span><span class="sxs-lookup"><span data-stu-id="ef260-499">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="ef260-500">Nella specifica ufficiale, la memorizzazione nella cache è finalizzata a ridurre la latenza e il sovraccarico di rete per soddisfare le richieste in una rete di client, proxy e server.</span><span class="sxs-lookup"><span data-stu-id="ef260-500">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="ef260-501">Non è necessariamente un modo per controllare il carico su un server di origine.</span><span class="sxs-lookup"><span data-stu-id="ef260-501">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="ef260-502">Il comportamento di memorizzazione nella cache non è disponibile per gli sviluppatori quando si usa il [middleware di memorizzazione nella cache delle risposte](xref:performance/caching/middleware) , perché il middleware rispetta la specifica di Caching ufficiale.</span><span class="sxs-lookup"><span data-stu-id="ef260-502">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="ef260-503">I [miglioramenti pianificati per il middleware](https://github.com/dotnet/AspNetCore/issues/2612) sono un'opportunità per configurare il middleware in modo da ignorare l'intestazione di una richiesta `Cache-Control` quando si decide di gestire una risposta memorizzata nella cache.</span><span class="sxs-lookup"><span data-stu-id="ef260-503">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="ef260-504">I miglioramenti pianificati offrono la possibilità di controllare meglio il carico del server.</span><span class="sxs-lookup"><span data-stu-id="ef260-504">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="ef260-505">Altra tecnologia di memorizzazione nella cache in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef260-505">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="ef260-506">Memorizzazione nella cache in memoria</span><span class="sxs-lookup"><span data-stu-id="ef260-506">In-memory caching</span></span>

<span data-ttu-id="ef260-507">La memorizzazione nella cache in memoria usa la memoria del server per archiviare i dati memorizzati nella cache.</span><span class="sxs-lookup"><span data-stu-id="ef260-507">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="ef260-508">Questo tipo di memorizzazione nella cache è adatto per un singolo server o più server che usano *sessioni permanenti*.</span><span class="sxs-lookup"><span data-stu-id="ef260-508">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="ef260-509">Sessioni permanenti significa che le richieste provenienti da un client vengono sempre indirizzate allo stesso server per l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="ef260-509">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="ef260-510">Per altre informazioni, vedere <xref:performance/caching/memory>.</span><span class="sxs-lookup"><span data-stu-id="ef260-510">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="ef260-511">Cache distribuita</span><span class="sxs-lookup"><span data-stu-id="ef260-511">Distributed Cache</span></span>

<span data-ttu-id="ef260-512">Usare una cache distribuita per archiviare i dati in memoria quando l'app è ospitata in un cloud o in server farm.</span><span class="sxs-lookup"><span data-stu-id="ef260-512">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="ef260-513">La cache è condivisa tra i server che elaborano le richieste.</span><span class="sxs-lookup"><span data-stu-id="ef260-513">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="ef260-514">Un client può inviare una richiesta gestita da qualsiasi server del gruppo se sono disponibili dati memorizzati nella cache per il client.</span><span class="sxs-lookup"><span data-stu-id="ef260-514">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="ef260-515">ASP.NET Core funziona con le cache distribuite SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)e [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .</span><span class="sxs-lookup"><span data-stu-id="ef260-515">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="ef260-516">Per altre informazioni, vedere <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="ef260-516">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="ef260-517">Helper per tag di cache</span><span class="sxs-lookup"><span data-stu-id="ef260-517">Cache Tag Helper</span></span>

<span data-ttu-id="ef260-518">Memorizza nella cache il contenuto di una visualizzazione o Razor di una pagina MVC con l'helper tag di cache.</span><span class="sxs-lookup"><span data-stu-id="ef260-518">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="ef260-519">L'helper tag di cache usa la memorizzazione nella cache in memoria per archiviare i dati.</span><span class="sxs-lookup"><span data-stu-id="ef260-519">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="ef260-520">Per altre informazioni, vedere <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="ef260-520">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="ef260-521">Helper tag di cache distribuita</span><span class="sxs-lookup"><span data-stu-id="ef260-521">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="ef260-522">Consente di memorizzare nella cache il contenuto di una visualizzazione o Razor di una pagina MVC negli scenari cloud distribuito o Web farm con l'helper tag di cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="ef260-522">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="ef260-523">L'helper tag di cache distribuita usa SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)o [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) per archiviare i dati.</span><span class="sxs-lookup"><span data-stu-id="ef260-523">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="ef260-524">Per altre informazioni, vedere <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="ef260-524">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="ef260-525">Attributo ResponseCache</span><span class="sxs-lookup"><span data-stu-id="ef260-525">ResponseCache attribute</span></span>

<span data-ttu-id="ef260-526"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Specifica i parametri necessari per impostare le intestazioni appropriate nella memorizzazione nella cache delle risposte.</span><span class="sxs-lookup"><span data-stu-id="ef260-526">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="ef260-527">Disabilitare la memorizzazione nella cache per il contenuto che contiene informazioni per i client autenticati.</span><span class="sxs-lookup"><span data-stu-id="ef260-527">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="ef260-528">La memorizzazione nella cache deve essere abilitata solo per il contenuto che non cambia in base all'identità di un utente o se un utente ha eseguito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="ef260-528">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="ef260-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>varia la risposta memorizzata in base ai valori dell'elenco specificato di chiavi di query.</span><span class="sxs-lookup"><span data-stu-id="ef260-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="ef260-530">Quando viene specificato un singolo valore `*` , il middleware varia le risposte in base a tutti i parametri della stringa di query della richiesta.</span><span class="sxs-lookup"><span data-stu-id="ef260-530">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="ef260-531">Il [middleware di memorizzazione nella cache delle risposte](xref:performance/caching/middleware) deve essere abilitato per impostare la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> Proprietà.</span><span class="sxs-lookup"><span data-stu-id="ef260-531">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="ef260-532">In caso contrario, viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ef260-532">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="ef260-533">Non esiste un'intestazione HTTP corrispondente per la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> Proprietà.</span><span class="sxs-lookup"><span data-stu-id="ef260-533">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="ef260-534">La proprietà è una funzionalità HTTP gestita dal middleware di memorizzazione nella cache delle risposte.</span><span class="sxs-lookup"><span data-stu-id="ef260-534">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="ef260-535">Affinché il middleware possa gestire una risposta memorizzata nella cache, la stringa di query e il valore della stringa di query devono corrispondere a una richiesta precedente.</span><span class="sxs-lookup"><span data-stu-id="ef260-535">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="ef260-536">Si consideri, ad esempio, la sequenza di richieste e i risultati mostrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="ef260-536">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="ef260-537">Richiesta</span><span class="sxs-lookup"><span data-stu-id="ef260-537">Request</span></span>                          | <span data-ttu-id="ef260-538">Risultato</span><span class="sxs-lookup"><span data-stu-id="ef260-538">Result</span></span>                    |
| ---
<span data-ttu-id="ef260-539">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-540">'Blazor'</span></span>
- <span data-ttu-id="ef260-541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-541">'Identity'</span></span>
- <span data-ttu-id="ef260-542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-542">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-543">'Razor'</span></span>
- <span data-ttu-id="ef260-544">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-545">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-546">'Blazor'</span></span>
- <span data-ttu-id="ef260-547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-547">'Identity'</span></span>
- <span data-ttu-id="ef260-548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-548">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-549">'Razor'</span></span>
- <span data-ttu-id="ef260-550">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-551">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-552">'Blazor'</span></span>
- <span data-ttu-id="ef260-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-553">'Identity'</span></span>
- <span data-ttu-id="ef260-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-555">'Razor'</span></span>
- <span data-ttu-id="ef260-556">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-557">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-558">'Blazor'</span></span>
- <span data-ttu-id="ef260-559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-559">'Identity'</span></span>
- <span data-ttu-id="ef260-560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-560">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-561">'Razor'</span></span>
- <span data-ttu-id="ef260-562">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-563">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-564">'Blazor'</span></span>
- <span data-ttu-id="ef260-565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-565">'Identity'</span></span>
- <span data-ttu-id="ef260-566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-566">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-567">'Razor'</span></span>
- <span data-ttu-id="ef260-568">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-569">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-570">'Blazor'</span></span>
- <span data-ttu-id="ef260-571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-571">'Identity'</span></span>
- <span data-ttu-id="ef260-572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-572">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-573">'Razor'</span></span>
- <span data-ttu-id="ef260-574">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-575">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-576">'Blazor'</span></span>
- <span data-ttu-id="ef260-577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-577">'Identity'</span></span>
- <span data-ttu-id="ef260-578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-578">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-579">'Razor'</span></span>
- <span data-ttu-id="ef260-580">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-581">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-582">'Blazor'</span></span>
- <span data-ttu-id="ef260-583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-583">'Identity'</span></span>
- <span data-ttu-id="ef260-584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-584">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-585">'Razor'</span></span>
- <span data-ttu-id="ef260-586">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-586">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-587">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-588">'Blazor'</span></span>
- <span data-ttu-id="ef260-589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-589">'Identity'</span></span>
- <span data-ttu-id="ef260-590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-590">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-591">'Razor'</span></span>
- <span data-ttu-id="ef260-592">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-592">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-593">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-594">'Blazor'</span></span>
- <span data-ttu-id="ef260-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-595">'Identity'</span></span>
- <span data-ttu-id="ef260-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-597">'Razor'</span></span>
- <span data-ttu-id="ef260-598">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-599">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-600">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-600">'Blazor'</span></span>
- <span data-ttu-id="ef260-601">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-601">'Identity'</span></span>
- <span data-ttu-id="ef260-602">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-602">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-603">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-603">'Razor'</span></span>
- <span data-ttu-id="ef260-604">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-604">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-605">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-606">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-606">'Blazor'</span></span>
- <span data-ttu-id="ef260-607">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-607">'Identity'</span></span>
- <span data-ttu-id="ef260-608">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-608">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-609">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-609">'Razor'</span></span>
- <span data-ttu-id="ef260-610">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-610">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-611">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-612">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-612">'Blazor'</span></span>
- <span data-ttu-id="ef260-613">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-613">'Identity'</span></span>
- <span data-ttu-id="ef260-614">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-614">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-615">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-615">'Razor'</span></span>
- <span data-ttu-id="ef260-616">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-616">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-617">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-618">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-618">'Blazor'</span></span>
- <span data-ttu-id="ef260-619">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-619">'Identity'</span></span>
- <span data-ttu-id="ef260-620">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-620">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-621">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-621">'Razor'</span></span>
- <span data-ttu-id="ef260-622">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-622">'SignalR' uid:</span></span> 

<span data-ttu-id="ef260-623">---------------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-623">---------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-624">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-624">'Blazor'</span></span>
- <span data-ttu-id="ef260-625">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-625">'Identity'</span></span>
- <span data-ttu-id="ef260-626">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-626">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-627">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-627">'Razor'</span></span>
- <span data-ttu-id="ef260-628">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-628">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-629">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-630">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-630">'Blazor'</span></span>
- <span data-ttu-id="ef260-631">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-631">'Identity'</span></span>
- <span data-ttu-id="ef260-632">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-632">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-633">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-633">'Razor'</span></span>
- <span data-ttu-id="ef260-634">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-634">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-635">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-636">'Blazor'</span></span>
- <span data-ttu-id="ef260-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-637">'Identity'</span></span>
- <span data-ttu-id="ef260-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-639">'Razor'</span></span>
- <span data-ttu-id="ef260-640">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-641">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-642">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-642">'Blazor'</span></span>
- <span data-ttu-id="ef260-643">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-643">'Identity'</span></span>
- <span data-ttu-id="ef260-644">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-644">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-645">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-645">'Razor'</span></span>
- <span data-ttu-id="ef260-646">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-646">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-647">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-648">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-648">'Blazor'</span></span>
- <span data-ttu-id="ef260-649">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-649">'Identity'</span></span>
- <span data-ttu-id="ef260-650">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-650">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-651">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-651">'Razor'</span></span>
- <span data-ttu-id="ef260-652">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-652">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-653">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-654">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-654">'Blazor'</span></span>
- <span data-ttu-id="ef260-655">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-655">'Identity'</span></span>
- <span data-ttu-id="ef260-656">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-656">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-657">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-657">'Razor'</span></span>
- <span data-ttu-id="ef260-658">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-658">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-659">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-660">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-660">'Blazor'</span></span>
- <span data-ttu-id="ef260-661">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-661">'Identity'</span></span>
- <span data-ttu-id="ef260-662">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-662">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-663">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-663">'Razor'</span></span>
- <span data-ttu-id="ef260-664">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-664">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-665">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-666">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-666">'Blazor'</span></span>
- <span data-ttu-id="ef260-667">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-667">'Identity'</span></span>
- <span data-ttu-id="ef260-668">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-668">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-669">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-669">'Razor'</span></span>
- <span data-ttu-id="ef260-670">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-670">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-671">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-672">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-672">'Blazor'</span></span>
- <span data-ttu-id="ef260-673">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-673">'Identity'</span></span>
- <span data-ttu-id="ef260-674">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-674">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-675">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-675">'Razor'</span></span>
- <span data-ttu-id="ef260-676">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-676">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ef260-677">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ef260-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ef260-678">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ef260-678">'Blazor'</span></span>
- <span data-ttu-id="ef260-679">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ef260-679">'Identity'</span></span>
- <span data-ttu-id="ef260-680">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ef260-680">'Let's Encrypt'</span></span>
- <span data-ttu-id="ef260-681">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ef260-681">'Razor'</span></span>
- <span data-ttu-id="ef260-682">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ef260-682">'SignalR' uid:</span></span> 

<span data-ttu-id="ef260-683">------------- | | `http://example.com?key1=value1` | Restituito dal server.</span><span class="sxs-lookup"><span data-stu-id="ef260-683">------------- | | `http://example.com?key1=value1` | Returned from the server.</span></span> <span data-ttu-id="ef260-684">| | `http://example.com?key1=value1` | Restituito dal middleware.</span><span class="sxs-lookup"><span data-stu-id="ef260-684">| | `http://example.com?key1=value1` | Returned from middleware.</span></span> <span data-ttu-id="ef260-685">| | `http://example.com?key1=value2` | Restituito dal server.</span><span class="sxs-lookup"><span data-stu-id="ef260-685">| | `http://example.com?key1=value2` | Returned from the server.</span></span> |

<span data-ttu-id="ef260-686">La prima richiesta viene restituita dal server e memorizzata nella cache nel middleware.</span><span class="sxs-lookup"><span data-stu-id="ef260-686">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="ef260-687">La seconda richiesta viene restituita dal middleware perché la stringa di query corrisponde alla richiesta precedente.</span><span class="sxs-lookup"><span data-stu-id="ef260-687">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="ef260-688">La terza richiesta non si trovi nella cache middleware perché il valore della stringa di query non corrisponde a una richiesta precedente.</span><span class="sxs-lookup"><span data-stu-id="ef260-688">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="ef260-689"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Viene usato per configurare e creare (tramite <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> ) un oggetto `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` .</span><span class="sxs-lookup"><span data-stu-id="ef260-689">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="ef260-690">`ResponseCacheFilter`Esegue le operazioni di aggiornamento delle intestazioni HTTP e delle funzionalità appropriate della risposta.</span><span class="sxs-lookup"><span data-stu-id="ef260-690">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="ef260-691">Il filtro:</span><span class="sxs-lookup"><span data-stu-id="ef260-691">The filter:</span></span>

* <span data-ttu-id="ef260-692">Rimuove le intestazioni esistenti per `Vary` , `Cache-Control` e `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="ef260-692">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="ef260-693">Scrive le intestazioni appropriate in base alle proprietà impostate in <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .</span><span class="sxs-lookup"><span data-stu-id="ef260-693">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="ef260-694">Aggiorna la funzionalità HTTP di caching delle risposte se <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> è impostato.</span><span class="sxs-lookup"><span data-stu-id="ef260-694">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="ef260-695">Variare</span><span class="sxs-lookup"><span data-stu-id="ef260-695">Vary</span></span>

<span data-ttu-id="ef260-696">Questa intestazione viene scritta solo quando la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> proprietà è impostata.</span><span class="sxs-lookup"><span data-stu-id="ef260-696">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="ef260-697">Proprietà impostata sul valore della `Vary` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="ef260-697">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="ef260-698">Nell'esempio seguente viene utilizzata la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> proprietà:</span><span class="sxs-lookup"><span data-stu-id="ef260-698">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="ef260-699">Usando l'app di esempio, visualizzare le intestazioni della risposta con gli strumenti di rete del browser.</span><span class="sxs-lookup"><span data-stu-id="ef260-699">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="ef260-700">Le intestazioni di risposta seguenti vengono inviate con la risposta della pagina cache1:</span><span class="sxs-lookup"><span data-stu-id="ef260-700">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="ef260-701">NoStore e location. None</span><span class="sxs-lookup"><span data-stu-id="ef260-701">NoStore and Location.None</span></span>

<span data-ttu-id="ef260-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>esegue l'override della maggior parte delle altre proprietà.</span><span class="sxs-lookup"><span data-stu-id="ef260-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="ef260-703">Quando questa proprietà è impostata su `true` , l' `Cache-Control` intestazione viene impostata su `no-store` .</span><span class="sxs-lookup"><span data-stu-id="ef260-703">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="ef260-704">Se <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> è impostato su `None` :</span><span class="sxs-lookup"><span data-stu-id="ef260-704">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="ef260-705">`Cache-Control` è impostato su `no-store,no-cache`.</span><span class="sxs-lookup"><span data-stu-id="ef260-705">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="ef260-706">`Pragma` è impostato su `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="ef260-706">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="ef260-707">Se <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> è `false` e <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> è `None` , `Cache-Control` e `Pragma` sono impostati su `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="ef260-707">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="ef260-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>viene in genere impostato su `true` per le pagine di errore.</span><span class="sxs-lookup"><span data-stu-id="ef260-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="ef260-709">La pagina cache2 nell'app di esempio genera intestazioni di risposta che indicano al client di non archiviare la risposta.</span><span class="sxs-lookup"><span data-stu-id="ef260-709">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="ef260-710">L'app di esempio restituisce la pagina cache2 con le intestazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="ef260-710">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="ef260-711">Località e durata</span><span class="sxs-lookup"><span data-stu-id="ef260-711">Location and Duration</span></span>

<span data-ttu-id="ef260-712">Per abilitare la memorizzazione nella cache, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> deve essere impostato su un valore positivo e <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> deve essere `Any` (valore predefinito) o `Client` .</span><span class="sxs-lookup"><span data-stu-id="ef260-712">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="ef260-713">Il Framework imposta l' `Cache-Control` intestazione sul valore della posizione seguito dalla `max-age` della risposta.</span><span class="sxs-lookup"><span data-stu-id="ef260-713">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="ef260-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>le opzioni di `Any` e vengono `Client` convertite in `Cache-Control` valori di intestazione `public` rispettivamente di e `private` .</span><span class="sxs-lookup"><span data-stu-id="ef260-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="ef260-715">Come indicato nella sezione [NoStore e location. None](#nostore-and-locationnone) , l'impostazione <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> di su `None` imposta entrambe `Cache-Control` le `Pragma` intestazioni e su `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="ef260-715">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="ef260-716">`Location.Any`( `Cache-Control` impostato su `public` ) indica che il *client o qualsiasi proxy intermedio* può memorizzare nella cache il valore, incluso il [middleware di memorizzazione nella cache delle risposte](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="ef260-716">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="ef260-717">`Location.Client`( `Cache-Control` impostato su `private` ) indica che *solo il client* può memorizzare nella cache il valore.</span><span class="sxs-lookup"><span data-stu-id="ef260-717">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="ef260-718">Nessuna cache intermedia deve memorizzare nella cache il valore, incluso il [middleware di memorizzazione nella cache delle risposte](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="ef260-718">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="ef260-719">Le intestazioni di controllo della cache forniscono semplicemente indicazioni ai client e ai proxy intermedi quando e come memorizzare nella cache le risposte.</span><span class="sxs-lookup"><span data-stu-id="ef260-719">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="ef260-720">Non vi è alcuna garanzia che i client e i proxy soddisfino la [specifica HTTP 1,1 Caching](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="ef260-720">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="ef260-721">Il [middleware di memorizzazione nella cache delle risposte](xref:performance/caching/middleware) segue sempre le regole di memorizzazione nella cache definite dalla specifica.</span><span class="sxs-lookup"><span data-stu-id="ef260-721">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="ef260-722">L'esempio seguente mostra il modello di pagina Cache3 dall'app di esempio e le intestazioni prodotte impostando <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> e lasciando il <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> valore predefinito:</span><span class="sxs-lookup"><span data-stu-id="ef260-722">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="ef260-723">L'app di esempio restituisce la pagina Cache3 con l'intestazione seguente:</span><span class="sxs-lookup"><span data-stu-id="ef260-723">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="ef260-724">Profili cache</span><span class="sxs-lookup"><span data-stu-id="ef260-724">Cache profiles</span></span>

<span data-ttu-id="ef260-725">Anziché duplicare le impostazioni della cache di risposta su molti attributi dell'azione del controller, è possibile configurare i profili della cache come opzioni durante la configurazione di MVC/ Razor pagine in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ef260-725">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ef260-726">I valori trovati in un profilo della cache a cui viene fatto riferimento vengono usati come valori predefiniti da <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> e vengono sottoposti a override da qualsiasi proprietà specificata nell'attributo.</span><span class="sxs-lookup"><span data-stu-id="ef260-726">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="ef260-727">Configurare un profilo della cache.</span><span class="sxs-lookup"><span data-stu-id="ef260-727">Set up a cache profile.</span></span> <span data-ttu-id="ef260-728">L'esempio seguente mostra un profilo della cache di 30 secondi nell'app di esempio `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ef260-728">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="ef260-729">Il modello di pagina Cache4 dell'app di esempio fa riferimento al `Default30` profilo della cache:</span><span class="sxs-lookup"><span data-stu-id="ef260-729">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="ef260-730">Il <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> può essere applicato a:</span><span class="sxs-lookup"><span data-stu-id="ef260-730">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* Razor<span data-ttu-id="ef260-731">Gestori di pagine (classi): gli attributi non possono essere applicati ai metodi del gestore.</span><span class="sxs-lookup"><span data-stu-id="ef260-731"> Page handlers (classes): Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="ef260-732">Controller MVC (classi).</span><span class="sxs-lookup"><span data-stu-id="ef260-732">MVC controllers (classes).</span></span>
* <span data-ttu-id="ef260-733">Azioni MVC (metodi): gli attributi a livello di metodo eseguono l'override delle impostazioni specificate negli attributi a livello di classe.</span><span class="sxs-lookup"><span data-stu-id="ef260-733">MVC actions (methods): Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="ef260-734">Intestazione risultante applicata alla risposta della pagina Cache4 dal `Default30` profilo della cache:</span><span class="sxs-lookup"><span data-stu-id="ef260-734">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="ef260-735">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="ef260-735">Additional resources</span></span>

* [<span data-ttu-id="ef260-736">Archiviazione delle risposte nelle cache</span><span class="sxs-lookup"><span data-stu-id="ef260-736">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="ef260-737">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="ef260-737">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
