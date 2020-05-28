---
<span data-ttu-id="e8573-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-102">'Blazor'</span></span>
- <span data-ttu-id="e8573-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-103">'Identity'</span></span>
- <span data-ttu-id="e8573-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-105">'Razor'</span></span>
- <span data-ttu-id="e8573-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-106">'SignalR' uid:</span></span> 

---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="e8573-107">Confrontare servizi gRPC e API HTTP</span><span class="sxs-lookup"><span data-stu-id="e8573-107">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="e8573-108">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="e8573-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="e8573-109">Questo articolo illustra in che modo i [servizi di gRPC](https://grpc.io/docs/guides/) vengono confrontati con le API HTTP con JSON (incluse ASP.NET Core [API Web](xref:web-api/index)).</span><span class="sxs-lookup"><span data-stu-id="e8573-109">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="e8573-110">La tecnologia usata per fornire un'API per l'app è una scelta importante e gRPC offre vantaggi esclusivi rispetto alle API HTTP.</span><span class="sxs-lookup"><span data-stu-id="e8573-110">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="e8573-111">Questo articolo illustra i punti di forza e i punti deboli di gRPC e consiglia scenari per l'uso di gRPC su altre tecnologie.</span><span class="sxs-lookup"><span data-stu-id="e8573-111">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="e8573-112">Confronto di alto livello</span><span class="sxs-lookup"><span data-stu-id="e8573-112">High-level comparison</span></span>

<span data-ttu-id="e8573-113">La tabella seguente offre un confronto di alto livello tra le funzionalità tra gRPC e le API HTTP con JSON.</span><span class="sxs-lookup"><span data-stu-id="e8573-113">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="e8573-114">Feature</span><span class="sxs-lookup"><span data-stu-id="e8573-114">Feature</span></span>          | <span data-ttu-id="e8573-115">gRPC</span><span class="sxs-lookup"><span data-stu-id="e8573-115">gRPC</span></span>                                               | <span data-ttu-id="e8573-116">API HTTP con JSON</span><span class="sxs-lookup"><span data-stu-id="e8573-116">HTTP APIs with JSON</span></span>           |
| ---
<span data-ttu-id="e8573-117">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-118">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-118">'Blazor'</span></span>
- <span data-ttu-id="e8573-119">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-119">'Identity'</span></span>
- <span data-ttu-id="e8573-120">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-120">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-121">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-121">'Razor'</span></span>
- <span data-ttu-id="e8573-122">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-122">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-123">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-124">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-124">'Blazor'</span></span>
- <span data-ttu-id="e8573-125">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-125">'Identity'</span></span>
- <span data-ttu-id="e8573-126">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-126">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-127">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-127">'Razor'</span></span>
- <span data-ttu-id="e8573-128">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-128">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-129">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-130">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-130">'Blazor'</span></span>
- <span data-ttu-id="e8573-131">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-131">'Identity'</span></span>
- <span data-ttu-id="e8573-132">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-132">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-133">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-133">'Razor'</span></span>
- <span data-ttu-id="e8573-134">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-134">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-135">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-136">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-136">'Blazor'</span></span>
- <span data-ttu-id="e8573-137">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-137">'Identity'</span></span>
- <span data-ttu-id="e8573-138">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-138">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-139">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-139">'Razor'</span></span>
- <span data-ttu-id="e8573-140">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-140">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-141">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-142">'Blazor'</span></span>
- <span data-ttu-id="e8573-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-143">'Identity'</span></span>
- <span data-ttu-id="e8573-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-145">'Razor'</span></span>
- <span data-ttu-id="e8573-146">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-146">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-147">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-148">'Blazor'</span></span>
- <span data-ttu-id="e8573-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-149">'Identity'</span></span>
- <span data-ttu-id="e8573-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-151">'Razor'</span></span>
- <span data-ttu-id="e8573-152">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-152">'SignalR' uid:</span></span> 

<span data-ttu-id="e8573-153">-------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-154">'Blazor'</span></span>
- <span data-ttu-id="e8573-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-155">'Identity'</span></span>
- <span data-ttu-id="e8573-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-157">'Razor'</span></span>
- <span data-ttu-id="e8573-158">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-159">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-160">'Blazor'</span></span>
- <span data-ttu-id="e8573-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-161">'Identity'</span></span>
- <span data-ttu-id="e8573-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-163">'Razor'</span></span>
- <span data-ttu-id="e8573-164">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-165">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-166">'Blazor'</span></span>
- <span data-ttu-id="e8573-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-167">'Identity'</span></span>
- <span data-ttu-id="e8573-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-169">'Razor'</span></span>
- <span data-ttu-id="e8573-170">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-171">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-172">'Blazor'</span></span>
- <span data-ttu-id="e8573-173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-173">'Identity'</span></span>
- <span data-ttu-id="e8573-174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-174">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-175">'Razor'</span></span>
- <span data-ttu-id="e8573-176">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-177">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-178">'Blazor'</span></span>
- <span data-ttu-id="e8573-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-179">'Identity'</span></span>
- <span data-ttu-id="e8573-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-181">'Razor'</span></span>
- <span data-ttu-id="e8573-182">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-183">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-184">'Blazor'</span></span>
- <span data-ttu-id="e8573-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-185">'Identity'</span></span>
- <span data-ttu-id="e8573-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-187">'Razor'</span></span>
- <span data-ttu-id="e8573-188">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-189">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-190">'Blazor'</span></span>
- <span data-ttu-id="e8573-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-191">'Identity'</span></span>
- <span data-ttu-id="e8573-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-193">'Razor'</span></span>
- <span data-ttu-id="e8573-194">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-195">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-196">'Blazor'</span></span>
- <span data-ttu-id="e8573-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-197">'Identity'</span></span>
- <span data-ttu-id="e8573-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-199">'Razor'</span></span>
- <span data-ttu-id="e8573-200">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-201">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-202">'Blazor'</span></span>
- <span data-ttu-id="e8573-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-203">'Identity'</span></span>
- <span data-ttu-id="e8573-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-205">'Razor'</span></span>
- <span data-ttu-id="e8573-206">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-207">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-208">'Blazor'</span></span>
- <span data-ttu-id="e8573-209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-209">'Identity'</span></span>
- <span data-ttu-id="e8573-210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-210">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-211">'Razor'</span></span>
- <span data-ttu-id="e8573-212">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-213">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-214">'Blazor'</span></span>
- <span data-ttu-id="e8573-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-215">'Identity'</span></span>
- <span data-ttu-id="e8573-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-217">'Razor'</span></span>
- <span data-ttu-id="e8573-218">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-219">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-219">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-220">'Blazor'</span></span>
- <span data-ttu-id="e8573-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-221">'Identity'</span></span>
- <span data-ttu-id="e8573-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-223">'Razor'</span></span>
- <span data-ttu-id="e8573-224">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-225">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-225">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-226">'Blazor'</span></span>
- <span data-ttu-id="e8573-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-227">'Identity'</span></span>
- <span data-ttu-id="e8573-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-229">'Razor'</span></span>
- <span data-ttu-id="e8573-230">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-231">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-231">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-232">'Blazor'</span></span>
- <span data-ttu-id="e8573-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-233">'Identity'</span></span>
- <span data-ttu-id="e8573-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-235">'Razor'</span></span>
- <span data-ttu-id="e8573-236">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-237">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-237">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-238">'Blazor'</span></span>
- <span data-ttu-id="e8573-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-239">'Identity'</span></span>
- <span data-ttu-id="e8573-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-241">'Razor'</span></span>
- <span data-ttu-id="e8573-242">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-243">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-243">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-244">'Blazor'</span></span>
- <span data-ttu-id="e8573-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-245">'Identity'</span></span>
- <span data-ttu-id="e8573-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-247">'Razor'</span></span>
- <span data-ttu-id="e8573-248">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-249">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-249">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-250">'Blazor'</span></span>
- <span data-ttu-id="e8573-251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-251">'Identity'</span></span>
- <span data-ttu-id="e8573-252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-252">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-253">'Razor'</span></span>
- <span data-ttu-id="e8573-254">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-255">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-255">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-256">'Blazor'</span></span>
- <span data-ttu-id="e8573-257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-257">'Identity'</span></span>
- <span data-ttu-id="e8573-258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-258">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-259">'Razor'</span></span>
- <span data-ttu-id="e8573-260">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-261">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-261">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-262">'Blazor'</span></span>
- <span data-ttu-id="e8573-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-263">'Identity'</span></span>
- <span data-ttu-id="e8573-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-265">'Razor'</span></span>
- <span data-ttu-id="e8573-266">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-267">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-267">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-268">'Blazor'</span></span>
- <span data-ttu-id="e8573-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-269">'Identity'</span></span>
- <span data-ttu-id="e8573-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-271">'Razor'</span></span>
- <span data-ttu-id="e8573-272">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-273">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-273">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-274">'Blazor'</span></span>
- <span data-ttu-id="e8573-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-275">'Identity'</span></span>
- <span data-ttu-id="e8573-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-277">'Razor'</span></span>
- <span data-ttu-id="e8573-278">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-279">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-279">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-280">'Blazor'</span></span>
- <span data-ttu-id="e8573-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-281">'Identity'</span></span>
- <span data-ttu-id="e8573-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-283">'Razor'</span></span>
- <span data-ttu-id="e8573-284">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-285">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-285">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-286">'Blazor'</span></span>
- <span data-ttu-id="e8573-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-287">'Identity'</span></span>
- <span data-ttu-id="e8573-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-289">'Razor'</span></span>
- <span data-ttu-id="e8573-290">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-290">'SignalR' uid:</span></span> 

<span data-ttu-id="e8573-291">------------------------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-291">------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-292">'Blazor'</span></span>
- <span data-ttu-id="e8573-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-293">'Identity'</span></span>
- <span data-ttu-id="e8573-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-295">'Razor'</span></span>
- <span data-ttu-id="e8573-296">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-297">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-297">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-298">'Blazor'</span></span>
- <span data-ttu-id="e8573-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-299">'Identity'</span></span>
- <span data-ttu-id="e8573-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-301">'Razor'</span></span>
- <span data-ttu-id="e8573-302">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-303">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-303">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-304">'Blazor'</span></span>
- <span data-ttu-id="e8573-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-305">'Identity'</span></span>
- <span data-ttu-id="e8573-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-307">'Razor'</span></span>
- <span data-ttu-id="e8573-308">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-309">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-309">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-310">'Blazor'</span></span>
- <span data-ttu-id="e8573-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-311">'Identity'</span></span>
- <span data-ttu-id="e8573-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-313">'Razor'</span></span>
- <span data-ttu-id="e8573-314">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-315">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-315">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-316">'Blazor'</span></span>
- <span data-ttu-id="e8573-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-317">'Identity'</span></span>
- <span data-ttu-id="e8573-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-319">'Razor'</span></span>
- <span data-ttu-id="e8573-320">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-321">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-321">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-322">'Blazor'</span></span>
- <span data-ttu-id="e8573-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-323">'Identity'</span></span>
- <span data-ttu-id="e8573-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-325">'Razor'</span></span>
- <span data-ttu-id="e8573-326">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-327">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-327">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-328">'Blazor'</span></span>
- <span data-ttu-id="e8573-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-329">'Identity'</span></span>
- <span data-ttu-id="e8573-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-331">'Razor'</span></span>
- <span data-ttu-id="e8573-332">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-333">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-333">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-334">'Blazor'</span></span>
- <span data-ttu-id="e8573-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-335">'Identity'</span></span>
- <span data-ttu-id="e8573-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-337">'Razor'</span></span>
- <span data-ttu-id="e8573-338">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-339">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-339">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-340">'Blazor'</span></span>
- <span data-ttu-id="e8573-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-341">'Identity'</span></span>
- <span data-ttu-id="e8573-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-343">'Razor'</span></span>
- <span data-ttu-id="e8573-344">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-345">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-345">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-346">'Blazor'</span></span>
- <span data-ttu-id="e8573-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-347">'Identity'</span></span>
- <span data-ttu-id="e8573-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-349">'Razor'</span></span>
- <span data-ttu-id="e8573-350">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-351">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-351">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-352">'Blazor'</span></span>
- <span data-ttu-id="e8573-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-353">'Identity'</span></span>
- <span data-ttu-id="e8573-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-355">'Razor'</span></span>
- <span data-ttu-id="e8573-356">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e8573-357">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e8573-357">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8573-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8573-358">'Blazor'</span></span>
- <span data-ttu-id="e8573-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8573-359">'Identity'</span></span>
- <span data-ttu-id="e8573-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8573-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8573-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8573-361">'Razor'</span></span>
- <span data-ttu-id="e8573-362">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="e8573-362">'SignalR' uid:</span></span> 

<span data-ttu-id="e8573-363">--------------- | | Contratto | Obbligatorio (*. proto*) | Facoltativo (OpenAPI) | | Protocollo | HTTP/2 | HTTP | | Payload | [Protobuf (Small, Binary)](#performance) | JSON (grande, leggibile) | | Prescriptiveness | [Specifica Strict](#strict-specification) | Sciolto.</span><span class="sxs-lookup"><span data-stu-id="e8573-363">--------------- | | Contract         | Required (*.proto*)                                | Optional (OpenAPI)            | | Protocol         | HTTP/2                                             | HTTP                          | | Payload          | [Protobuf (small, binary)](#performance)           | JSON (large, human readable)  | | Prescriptiveness | [Strict specification](#strict-specification)      | Loose.</span></span> <span data-ttu-id="e8573-364">Qualsiasi HTTP è valido.</span><span class="sxs-lookup"><span data-stu-id="e8573-364">Any HTTP is valid.</span></span>     <span data-ttu-id="e8573-365">| | Flusso | [Client, server, bidirezionale](#streaming) | Client, server | | Supporto browser | [No (richiede grpc-Web)](#limited-browser-support) | Sì | | Sicurezza | Trasporto (TLS) | Trasporto (TLS) | | Generazione di codice client | [Sì](#code-generation) | OpenAPI + strumenti di terze parti |</span><span class="sxs-lookup"><span data-stu-id="e8573-365">| | Streaming        | [Client, server, bi-directional](#streaming)       | Client, server                | | Browser support  | [No (requires grpc-web)](#limited-browser-support) | Yes                           | | Security         | Transport (TLS)                                    | Transport (TLS)               | | Client code-generation | [Yes](#code-generation)                      | OpenAPI + third-party tooling |</span></span>

## <a name="grpc-strengths"></a><span data-ttu-id="e8573-366">punti di forza di gRPC</span><span class="sxs-lookup"><span data-stu-id="e8573-366">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="e8573-367">Prestazioni</span><span class="sxs-lookup"><span data-stu-id="e8573-367">Performance</span></span>

<span data-ttu-id="e8573-368">i messaggi gRPC vengono serializzati tramite [protobuf](https://developers.google.com/protocol-buffers/docs/overview), un formato di messaggio binario efficiente.</span><span class="sxs-lookup"><span data-stu-id="e8573-368">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="e8573-369">Protobuf serializza molto rapidamente sul server e sul client.</span><span class="sxs-lookup"><span data-stu-id="e8573-369">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="e8573-370">La serializzazione protobuf produce payload di messaggi di piccole dimensioni, importanti in scenari con larghezza di banda limitata come le app per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="e8573-370">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="e8573-371">gRPC è progettato per HTTP/2, una revisione principale del protocollo HTTP che offre vantaggi significativi in merito alle prestazioni rispetto a HTTP 1. x:</span><span class="sxs-lookup"><span data-stu-id="e8573-371">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="e8573-372">Framing e compressione binaria.</span><span class="sxs-lookup"><span data-stu-id="e8573-372">Binary framing and compression.</span></span> <span data-ttu-id="e8573-373">Il protocollo HTTP/2 è compatto ed efficiente sia per l'invio sia per la ricezione.</span><span class="sxs-lookup"><span data-stu-id="e8573-373">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="e8573-374">Multiplexing di più chiamate HTTP/2 su una singola connessione TCP.</span><span class="sxs-lookup"><span data-stu-id="e8573-374">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="e8573-375">Il multiplexing Elimina il [blocco Head-of-line](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span><span class="sxs-lookup"><span data-stu-id="e8573-375">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="e8573-376">HTTP/2 non è esclusivo per gRPC.</span><span class="sxs-lookup"><span data-stu-id="e8573-376">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="e8573-377">Molti tipi di richiesta, incluse le API HTTP con JSON, possono usare HTTP/2 e trarre vantaggio dai miglioramenti delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="e8573-377">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="e8573-378">Generazione del codice</span><span class="sxs-lookup"><span data-stu-id="e8573-378">Code generation</span></span>

<span data-ttu-id="e8573-379">Tutti i Framework gRPC offrono supporto di prima classe per la generazione di codice.</span><span class="sxs-lookup"><span data-stu-id="e8573-379">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="e8573-380">Un file principale per lo sviluppo gRPC è il [file. proto](https://developers.google.com/protocol-buffers/docs/proto3), che definisce il contratto di servizi e messaggi di gRPC.</span><span class="sxs-lookup"><span data-stu-id="e8573-380">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="e8573-381">Da questo file gRPC Frameworks codice genererà una classe di base del servizio, i messaggi e un client completo.</span><span class="sxs-lookup"><span data-stu-id="e8573-381">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="e8573-382">Condividendo il file con *estensione proto* tra il server e il client, i messaggi e il codice client possono essere generati da end-to-end.</span><span class="sxs-lookup"><span data-stu-id="e8573-382">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="e8573-383">La generazione di codice del client elimina la duplicazione dei messaggi nel client e nel server e crea automaticamente un client fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="e8573-383">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="e8573-384">Non è necessario scrivere un client per risparmiare tempo di sviluppo significativo nelle applicazioni con molti servizi.</span><span class="sxs-lookup"><span data-stu-id="e8573-384">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="e8573-385">Specifica Strict</span><span class="sxs-lookup"><span data-stu-id="e8573-385">Strict specification</span></span>

<span data-ttu-id="e8573-386">Una specifica formale per l'API HTTP con JSON non esiste.</span><span class="sxs-lookup"><span data-stu-id="e8573-386">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="e8573-387">Gli sviluppatori discutono del formato migliore di URL, verbi HTTP e codici di risposta.</span><span class="sxs-lookup"><span data-stu-id="e8573-387">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="e8573-388">La [specifica gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) è prescrittiva sul formato che deve essere seguito da un servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="e8573-388">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="e8573-389">gRPC Elimina il dibattito e Risparmia tempo per gli sviluppatori perché gRPC è coerente tra piattaforme e implementazioni.</span><span class="sxs-lookup"><span data-stu-id="e8573-389">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="e8573-390">Streaming</span><span class="sxs-lookup"><span data-stu-id="e8573-390">Streaming</span></span>

<span data-ttu-id="e8573-391">HTTP/2 fornisce una base per flussi di comunicazione di lunga durata e in tempo reale.</span><span class="sxs-lookup"><span data-stu-id="e8573-391">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="e8573-392">gRPC fornisce il supporto di prima classe per lo streaming tramite HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e8573-392">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="e8573-393">Un servizio gRPC supporta tutte le combinazioni di streaming:</span><span class="sxs-lookup"><span data-stu-id="e8573-393">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="e8573-394">Unario (nessun flusso)</span><span class="sxs-lookup"><span data-stu-id="e8573-394">Unary (no streaming)</span></span>
* <span data-ttu-id="e8573-395">Streaming da server a client</span><span class="sxs-lookup"><span data-stu-id="e8573-395">Server to client streaming</span></span>
* <span data-ttu-id="e8573-396">Streaming da client a server</span><span class="sxs-lookup"><span data-stu-id="e8573-396">Client to server streaming</span></span>
* <span data-ttu-id="e8573-397">Streaming bidirezionale</span><span class="sxs-lookup"><span data-stu-id="e8573-397">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="e8573-398">Scadenza/timeout e annullamento</span><span class="sxs-lookup"><span data-stu-id="e8573-398">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="e8573-399">gRPC consente ai client di specificare per quanto tempo sono disposti ad attendere il completamento di un RPC.</span><span class="sxs-lookup"><span data-stu-id="e8573-399">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="e8573-400">La [scadenza](https://grpc.io/blog/deadlines) viene inviata al server e il server può decidere quale azione intraprendere se supera la scadenza.</span><span class="sxs-lookup"><span data-stu-id="e8573-400">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="e8573-401">Ad esempio, il server potrebbe annullare le richieste gRPC/HTTP/database in corso in fase di timeout.</span><span class="sxs-lookup"><span data-stu-id="e8573-401">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="e8573-402">La propagazione della scadenza e dell'annullamento tramite chiamate gRPC figlio consente di applicare limiti di utilizzo delle risorse.</span><span class="sxs-lookup"><span data-stu-id="e8573-402">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="e8573-403">scenari consigliati gRPC</span><span class="sxs-lookup"><span data-stu-id="e8573-403">gRPC recommended scenarios</span></span>

<span data-ttu-id="e8573-404">gRPC è particolarmente adatto agli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="e8573-404">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="e8573-405">**Microservizi**: gRPC è progettato per la comunicazione con bassa latenza e velocità effettiva elevata.</span><span class="sxs-lookup"><span data-stu-id="e8573-405">**Microservices**: gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="e8573-406">gRPC è ideale per i microservizi leggeri in cui l'efficienza è fondamentale.</span><span class="sxs-lookup"><span data-stu-id="e8573-406">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="e8573-407">**Comunicazione in tempo reale da punto a punto**: gRPC offre un supporto eccellente per lo streaming bidirezionale.</span><span class="sxs-lookup"><span data-stu-id="e8573-407">**Point-to-point real-time communication**: gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="e8573-408">i servizi gRPC possono eseguire il push dei messaggi in tempo reale senza polling.</span><span class="sxs-lookup"><span data-stu-id="e8573-408">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="e8573-409">**Ambienti poliglotti**: gli strumenti gRPC supportano tutti i linguaggi di sviluppo più diffusi, rendendo gRPC una scelta ottimale per gli ambienti multilingue.</span><span class="sxs-lookup"><span data-stu-id="e8573-409">**Polyglot environments**: gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="e8573-410">**Ambienti vincolati alla rete**: i messaggi gRPC vengono serializzati con protobuf, un formato di messaggio leggero.</span><span class="sxs-lookup"><span data-stu-id="e8573-410">**Network constrained environments**: gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="e8573-411">Un messaggio gRPC è sempre più piccolo di un messaggio JSON equivalente.</span><span class="sxs-lookup"><span data-stu-id="e8573-411">A gRPC message is always smaller than an equivalent JSON message.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="e8573-412">punti deboli gRPC</span><span class="sxs-lookup"><span data-stu-id="e8573-412">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="e8573-413">Supporto browser limitato</span><span class="sxs-lookup"><span data-stu-id="e8573-413">Limited browser support</span></span>

<span data-ttu-id="e8573-414">Attualmente non è possibile chiamare direttamente un servizio gRPC da un browser.</span><span class="sxs-lookup"><span data-stu-id="e8573-414">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="e8573-415">gRPC USA in modo intensivo le funzionalità HTTP/2 e nessun browser fornisce il livello di controllo necessario per le richieste Web per supportare un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="e8573-415">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="e8573-416">I browser, ad esempio, non consentono a un chiamante di richiedere l'utilizzo di HTTP/2 o di fornire l'accesso ai frame HTTP/2 sottostanti.</span><span class="sxs-lookup"><span data-stu-id="e8573-416">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="e8573-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) è una tecnologia aggiuntiva del team gRPC che fornisce un supporto limitato per gRPC nel browser.</span><span class="sxs-lookup"><span data-stu-id="e8573-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides limited gRPC support in the browser.</span></span> <span data-ttu-id="e8573-418">gRPC-Web è costituito da due parti: un client JavaScript che supporta tutti i browser moderni e un proxy gRPC-Web nel server.</span><span class="sxs-lookup"><span data-stu-id="e8573-418">gRPC-Web consists of two parts: a JavaScript client that supports all modern browsers, and a gRPC-Web proxy on the server.</span></span> <span data-ttu-id="e8573-419">Il client gRPC-Web chiama il proxy e il proxy inoltrerà le richieste gRPC al server gRPC.</span><span class="sxs-lookup"><span data-stu-id="e8573-419">The gRPC-Web client calls the proxy and the proxy will forward on the gRPC requests to the gRPC server.</span></span>

<span data-ttu-id="e8573-420">Non tutte le funzionalità di gRPC sono supportate da gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="e8573-420">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="e8573-421">Il flusso client e bidirezionale non è supportato ed è disponibile un supporto limitato per lo streaming del server.</span><span class="sxs-lookup"><span data-stu-id="e8573-421">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

> [!TIP]
> <span data-ttu-id="e8573-422">.NET Core offre supporto sperimentale per gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="e8573-422">.NET Core has experimental support for gRPC-Web.</span></span> <span data-ttu-id="e8573-423"><xref:grpc/browser>Per ulteriori informazioni, visitare.</span><span class="sxs-lookup"><span data-stu-id="e8573-423">Visit <xref:grpc/browser> for more information.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="e8573-424">Non leggibile</span><span class="sxs-lookup"><span data-stu-id="e8573-424">Not human readable</span></span>

<span data-ttu-id="e8573-425">Le richieste API HTTP vengono inviate come testo e possono essere lette e create dagli utenti.</span><span class="sxs-lookup"><span data-stu-id="e8573-425">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="e8573-426">per impostazione predefinita, i messaggi gRPC vengono codificati con protobuf.</span><span class="sxs-lookup"><span data-stu-id="e8573-426">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="e8573-427">Sebbene protobuf sia efficace per l'invio e la ricezione, il formato binario non è leggibile.</span><span class="sxs-lookup"><span data-stu-id="e8573-427">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="e8573-428">Protobuf richiede la descrizione dell'interfaccia del messaggio specificata nel file *. proto* per la deserializzazione corretta.</span><span class="sxs-lookup"><span data-stu-id="e8573-428">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="e8573-429">È necessario disporre di strumenti aggiuntivi per analizzare i payload protobuf in transito e comporre le richieste manualmente.</span><span class="sxs-lookup"><span data-stu-id="e8573-429">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="e8573-430">Sono disponibili funzionalità quali [Reflection server](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) e lo [strumento da riga di comando gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) per supportare i messaggi protobuf binari.</span><span class="sxs-lookup"><span data-stu-id="e8573-430">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="e8573-431">Inoltre, i messaggi protobuf supportano la [conversione da e verso JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span><span class="sxs-lookup"><span data-stu-id="e8573-431">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="e8573-432">La conversione JSON incorporata consente di convertire in modo efficiente i messaggi protobuf da e verso il formato leggibile durante il debug.</span><span class="sxs-lookup"><span data-stu-id="e8573-432">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="e8573-433">Scenari di Framework alternativi</span><span class="sxs-lookup"><span data-stu-id="e8573-433">Alternative framework scenarios</span></span>

<span data-ttu-id="e8573-434">Gli altri Framework sono consigliati rispetto a gRPC negli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="e8573-434">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="e8573-435">**API accessibili dal browser**: gRPC non è completamente supportato nel browser.</span><span class="sxs-lookup"><span data-stu-id="e8573-435">**Browser accessible APIs**: gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="e8573-436">gRPC-Web può offrire il supporto del browser, ma presenta limitazioni e introduce un proxy server.</span><span class="sxs-lookup"><span data-stu-id="e8573-436">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="e8573-437">**Trasmissione in tempo reale della comunicazione**: gRPC supporta la comunicazione in tempo reale tramite streaming, ma il concetto di trasmissione di un messaggio alle connessioni registrate non esiste.</span><span class="sxs-lookup"><span data-stu-id="e8573-437">**Broadcast real-time communication**: gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="e8573-438">Ad esempio, in uno scenario di chat room in cui i nuovi messaggi di chat devono essere inviati a tutti i client nella chat room, ogni chiamata gRPC è necessaria per trasmettere singolarmente nuovi messaggi di chat al client.</span><span class="sxs-lookup"><span data-stu-id="e8573-438">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="e8573-439">[SignalR](xref:signalr/introduction)è un Framework utile per questo scenario.</span><span class="sxs-lookup"><span data-stu-id="e8573-439">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> SignalR<span data-ttu-id="e8573-440">include il concetto di connessioni permanenti e il supporto incorporato per la trasmissione di messaggi.</span><span class="sxs-lookup"><span data-stu-id="e8573-440"> has the concept of persistent connections and built-in support for broadcasting messages.</span></span>
* <span data-ttu-id="e8573-441">**Comunicazione tra processi**: un processo deve ospitare un server http/2 per accettare le chiamate gRPC in ingresso.</span><span class="sxs-lookup"><span data-stu-id="e8573-441">**Inter-process communication**: A process must host an HTTP/2 server to accept incoming gRPC calls.</span></span> <span data-ttu-id="e8573-442">Per Windows, le [pipe](/dotnet/standard/io/pipe-operations) di comunicazione tra processi sono un metodo di comunicazione rapido e leggero.</span><span class="sxs-lookup"><span data-stu-id="e8573-442">For Windows, inter-process communication [pipes](/dotnet/standard/io/pipe-operations) is a fast, lightweight method of communication.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8573-443">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e8573-443">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
