---
title: Perché eseguire la migrazione da WCF ad ASP.NET Core gRPC
author: markrendle
description: Questo articolo fornisce un riepilogo dei motivi per cui ASP.NET Core gRPC è una soluzione ideale per gli sviluppatori di Windows Communication Foundation (WCF) che desiderano eseguire la migrazione a architetture e piattaforme moderne.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058688"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a><span data-ttu-id="f7d82-103">gRPC per sviluppatori Windows Communication Foundation (WCF)</span><span class="sxs-lookup"><span data-stu-id="f7d82-103">gRPC for Windows Communication Foundation (WCF) developers</span></span>

<span data-ttu-id="f7d82-104">Questo articolo fornisce un riepilogo dei motivi per cui ASP.NET Core gRPC è una soluzione ideale per gli sviluppatori di Windows Communication Foundation (WCF) che desiderano eseguire la migrazione a architetture e piattaforme moderne.</span><span class="sxs-lookup"><span data-stu-id="f7d82-104">This article provides a summary of why ASP.NET Core gRPC is a good fit for Windows Communication Foundation (WCF) developers who want to migrate to modern architectures and platforms.</span></span>

## <a name="comparison-to-wcf"></a><span data-ttu-id="f7d82-105">Confronto con WCF</span><span class="sxs-lookup"><span data-stu-id="f7d82-105">Comparison to WCF</span></span>

<span data-ttu-id="f7d82-106">Sebbene l'implementazione e l'approccio siano diversi per gRPC, l'esperienza di sviluppo e utilizzo di servizi con gRPC dovrebbe essere intuitiva per gli sviluppatori WCF.</span><span class="sxs-lookup"><span data-stu-id="f7d82-106">Although the implementation and approach are different for gRPC, the experience of developing and consuming services with gRPC should be intuitive for WCF developers.</span></span> <span data-ttu-id="f7d82-107">WCF e gRPC sono Framework RPC (Remote Procedure Call) con gli stessi obiettivi:</span><span class="sxs-lookup"><span data-stu-id="f7d82-107">WCF and gRPC are RPC (remote procedure call) frameworks with the same goals:</span></span>

* <span data-ttu-id="f7d82-108">È possibile scrivere codice come se il client e il server si trovino nella stessa piattaforma.</span><span class="sxs-lookup"><span data-stu-id="f7d82-108">Make it possible to code as though the client and server are on the same platform.</span></span>
* <span data-ttu-id="f7d82-109">Fornire un'API di rete portatile semplificata.</span><span class="sxs-lookup"><span data-stu-id="f7d82-109">Provide a simplified portable networking API.</span></span>

<span data-ttu-id="f7d82-110">Entrambe le piattaforme condividono il requisito di dichiarare e implementare un'interfaccia, sebbene il processo di dichiarazione dell'interfaccia sia diverso.</span><span class="sxs-lookup"><span data-stu-id="f7d82-110">Both platforms share the requirement of declaring and implementing an interface, although the process for declaring the interface is different.</span></span> <span data-ttu-id="f7d82-111">Molti tipi di chiamate RPC supportate da gRPC vengono mappate correttamente ai binding disponibili per i servizi WCF.</span><span class="sxs-lookup"><span data-stu-id="f7d82-111">The many types of RPC calls that gRPC supports map well to the bindings available to WCF services.</span></span> <span data-ttu-id="f7d82-112">Per ulteriori informazioni ed esempi, vedere [eseguire la migrazione di una soluzione WCF a gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span><span class="sxs-lookup"><span data-stu-id="f7d82-112">For more information and examples, see [Migrate a WCF solution to gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span></span>

## <a name="benefits-of-grpc"></a><span data-ttu-id="f7d82-113">Vantaggi di gRPC</span><span class="sxs-lookup"><span data-stu-id="f7d82-113">Benefits of gRPC</span></span>

<span data-ttu-id="f7d82-114">gRPC fornisce un framework migliore rispetto ad altri approcci per i motivi seguenti.</span><span class="sxs-lookup"><span data-stu-id="f7d82-114">gRPC provides a better framework than other approaches for the following reasons.</span></span>

### <a name="performance"></a><span data-ttu-id="f7d82-115">Prestazioni</span><span class="sxs-lookup"><span data-stu-id="f7d82-115">Performance</span></span>

<span data-ttu-id="f7d82-116">gRPC Usa HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f7d82-116">gRPC uses HTTP/2.</span></span> <span data-ttu-id="f7d82-117">Diversamente da HTTP/1.1, HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="f7d82-117">In contrast to HTTP/1.1, HTTP/2:</span></span>

* <span data-ttu-id="f7d82-118">È un protocollo binario più piccolo e più veloce.</span><span class="sxs-lookup"><span data-stu-id="f7d82-118">Is a smaller, faster binary protocol.</span></span>
* <span data-ttu-id="f7d82-119">È più efficiente per l'analisi dei computer.</span><span class="sxs-lookup"><span data-stu-id="f7d82-119">Is more efficient for computers to parse.</span></span>
* <span data-ttu-id="f7d82-120">Supporta le richieste di multiplexing su una singola connessione.</span><span class="sxs-lookup"><span data-stu-id="f7d82-120">Supports multiplexing requests over a single connection.</span></span> <span data-ttu-id="f7d82-121">Il multiplexing consente l'invio di più richieste su una connessione senza che le richieste si blocchino a vicenda.</span><span class="sxs-lookup"><span data-stu-id="f7d82-121">Multiplexing enables multiple requests to be sent over one connection without requests blocking each other.</span></span> <span data-ttu-id="f7d82-122">In HTTP/1.1 il blocco è noto come "blocco Head-of-line (HOL)".</span><span class="sxs-lookup"><span data-stu-id="f7d82-122">In HTTP/1.1, the blocking is known as "head-of-line (HOL) blocking."</span></span>

<span data-ttu-id="f7d82-123">gRPC utilizza protobuf, un formato binario efficiente, per serializzare i messaggi.</span><span class="sxs-lookup"><span data-stu-id="f7d82-123">gRPC uses Protobuf, an efficient binary format, to serialize messages.</span></span> <span data-ttu-id="f7d82-124">I messaggi protobuf sono:</span><span class="sxs-lookup"><span data-stu-id="f7d82-124">Protobuf messages are:</span></span>
* <span data-ttu-id="f7d82-125">Veloce da serializzare e deserializzare.</span><span class="sxs-lookup"><span data-stu-id="f7d82-125">Fast to serialize and deserialize.</span></span>
* <span data-ttu-id="f7d82-126">Utilizzare una larghezza di banda inferiore rispetto ai formati basati su testo.</span><span class="sxs-lookup"><span data-stu-id="f7d82-126">Use less bandwidth than text-based formats.</span></span> 

<span data-ttu-id="f7d82-127">gRPC è una soluzione efficace per dispositivi mobili e reti con restrizioni della larghezza di banda.</span><span class="sxs-lookup"><span data-stu-id="f7d82-127">gRPC is a good solution for mobile devices and networks with bandwidth restrictions.</span></span>

### <a name="interoperability"></a><span data-ttu-id="f7d82-128">Interoperabilità</span><span class="sxs-lookup"><span data-stu-id="f7d82-128">Interoperability</span></span>

<span data-ttu-id="f7d82-129">Sono disponibili strumenti e librerie gRPC per tutti i principali linguaggi di programmazione e piattaforme, tra cui .NET, Java, Python, go, C++, Node.js, Swift, Dart, Ruby e PHP.</span><span class="sxs-lookup"><span data-stu-id="f7d82-129">There are gRPC tools and libraries for all major programming languages and platforms, including .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby, and PHP.</span></span> <span data-ttu-id="f7d82-130">Grazie al formato wire Binary protobuf e alla generazione efficiente del codice per ogni piattaforma, gli sviluppatori possono creare app multipiattaforma e ad alte prestazioni.</span><span class="sxs-lookup"><span data-stu-id="f7d82-130">Thanks to the Protobuf binary wire format and the efficient code generation for each platform, developers can build cross-platform, performant apps.</span></span>

### <a name="usability-and-productivity"></a><span data-ttu-id="f7d82-131">Usabilità e produttività</span><span class="sxs-lookup"><span data-stu-id="f7d82-131">Usability and productivity</span></span>

<span data-ttu-id="f7d82-132">gRPC è una soluzione RPC completa.</span><span class="sxs-lookup"><span data-stu-id="f7d82-132">gRPC is a comprehensive RPC solution.</span></span> <span data-ttu-id="f7d82-133">Funziona in modo coerente in più linguaggi e piattaforme.</span><span class="sxs-lookup"><span data-stu-id="f7d82-133">It works consistently across multiple languages and platforms.</span></span> <span data-ttu-id="f7d82-134">Fornisce inoltre strumenti eccellenti, con la maggior parte del codice standard generato automaticamente.</span><span class="sxs-lookup"><span data-stu-id="f7d82-134">It also provides excellent tooling, with much of the boilerplate code automatically generated.</span></span> <span data-ttu-id="f7d82-135">Analogamente a WCF, gRPC genera automaticamente messaggi e un client fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="f7d82-135">Like WCF, gRPC automatically generates messages and a strongly typed client.</span></span> <span data-ttu-id="f7d82-136">Il tempo degli sviluppatori viene liberato per concentrarsi sulla logica di business.</span><span class="sxs-lookup"><span data-stu-id="f7d82-136">Developer time is freed up to focus on business logic.</span></span>

### <a name="streaming"></a><span data-ttu-id="f7d82-137">Streaming</span><span class="sxs-lookup"><span data-stu-id="f7d82-137">Streaming</span></span>

<span data-ttu-id="f7d82-138">gRPC dispone di un flusso bidirezionale completo, che fornisce funzionalità simili ai servizi duplex completi di WCF.</span><span class="sxs-lookup"><span data-stu-id="f7d82-138">gRPC has full bidirectional streaming, which provides similar functionality to WCF's full duplex services.</span></span> <span data-ttu-id="f7d82-139">il flusso di gRPC può funzionare su connessioni Internet normali, servizi di bilanciamento del carico e mesh dei servizi.</span><span class="sxs-lookup"><span data-stu-id="f7d82-139">gRPC streaming can operate over regular internet connections, load balancers, and service meshes.</span></span>

### <a name="deadlines-timeouts-and-cancellation"></a><span data-ttu-id="f7d82-140">Scadenze, timeout e annullamento</span><span class="sxs-lookup"><span data-stu-id="f7d82-140">Deadlines, timeouts, and cancellation</span></span>

<span data-ttu-id="f7d82-141">gRPC consente ai client di specificare il tempo massimo per il completamento di una RPC.</span><span class="sxs-lookup"><span data-stu-id="f7d82-141">gRPC allows clients to specify a maximum time for an RPC to finish.</span></span> <span data-ttu-id="f7d82-142">Se viene superata la scadenza specificata, il server può annullare l'operazione indipendentemente dal client.</span><span class="sxs-lookup"><span data-stu-id="f7d82-142">If the specified deadline is exceeded, the server can cancel the operation independently of the client.</span></span> <span data-ttu-id="f7d82-143">Le scadenze e gli annullamenti possono essere propagati attraverso le successive chiamate gRPC per consentire l'applicazione dei limiti di utilizzo delle risorse.</span><span class="sxs-lookup"><span data-stu-id="f7d82-143">Deadlines and cancellations can be propagated through subsequent gRPC calls to help enforce resource usage limits.</span></span> <span data-ttu-id="f7d82-144">I client possono arrestare le operazioni quando viene superata una scadenza o in una versione precedente, se necessario.</span><span class="sxs-lookup"><span data-stu-id="f7d82-144">Clients can stop operations when a deadline is exceeded, or earlier if necessary.</span></span> <span data-ttu-id="f7d82-145">Ad esempio, i client possono arrestare le operazioni a causa di un'interazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="f7d82-145">For example, clients can stop operations because of a user interaction.</span></span>

### <a name="security"></a><span data-ttu-id="f7d82-146">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="f7d82-146">Security</span></span>

<span data-ttu-id="f7d82-147">gRPC può usare TLS e HTTP/2 per fornire una connessione crittografata end-to-end tra il client e il server.</span><span class="sxs-lookup"><span data-stu-id="f7d82-147">gRPC can use TLS and HTTP/2 to provide an end-to-end encrypted connection between the client and the server.</span></span> <span data-ttu-id="f7d82-148">Il supporto per l'autenticazione dei certificati client aumenta ulteriormente la sicurezza e il trust tra client e server.</span><span class="sxs-lookup"><span data-stu-id="f7d82-148">Support for client certificate authentication further increases security and trust between client and server.</span></span>

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a><span data-ttu-id="f7d82-149">gRPC come percorso di migrazione per WCF a .NET Core e .NET 5</span><span class="sxs-lookup"><span data-stu-id="f7d82-149">gRPC as a migration path for WCF to .NET Core and .NET 5</span></span>

<span data-ttu-id="f7d82-150">.NET Core e .NET 5 contrassegnano un cambiamento nel modo in cui Microsoft offre soluzioni di comunicazione remota agli sviluppatori che desiderano distribuire servizi in una vasta gamma di piattaforme.</span><span class="sxs-lookup"><span data-stu-id="f7d82-150">.NET Core and .NET 5 marks a shift in the way that Microsoft delivers remote communication solutions to developers who want to deliver services across a range of platforms.</span></span> <span data-ttu-id="f7d82-151">.NET Core e .NET 5 supportano la [chiamata ai servizi WCF](/dotnet/core/additional-tools/wcf-web-service-reference-guide), ma non offrono il supporto lato server per l'hosting di WCF.</span><span class="sxs-lookup"><span data-stu-id="f7d82-151">.NET Core and .NET 5 support [calling WCF services](/dotnet/core/additional-tools/wcf-web-service-reference-guide), but won't offer server-side support for hosting WCF.</span></span>

<span data-ttu-id="f7d82-152">Esistono due percorsi consigliati per la modernizzazione delle app WCF:</span><span class="sxs-lookup"><span data-stu-id="f7d82-152">There are two recommended paths for modernizing WCF apps:</span></span>

* <span data-ttu-id="f7d82-153">gRPC si basa su tecnologie moderne ed è emerso come la scelta più comune nella community di sviluppatori per le app RPC.</span><span class="sxs-lookup"><span data-stu-id="f7d82-153">gRPC is built on modern technologies and has emerged as the most popular choice across the developer community for RPC apps.</span></span> <span data-ttu-id="f7d82-154">A partire da .NET Core 3,0, le piattaforme .NET moderne offrono un supporto eccellente per gRPC.</span><span class="sxs-lookup"><span data-stu-id="f7d82-154">Starting with .NET Core 3.0, modern .NET platforms have excellent support for gRPC.</span></span> <span data-ttu-id="f7d82-155">La migrazione dei servizi WCF per l'uso di gRPC consente di fornire le funzionalità RPC, le prestazioni, un'interoperabilità necessaria nelle app moderne.</span><span class="sxs-lookup"><span data-stu-id="f7d82-155">Migrating WCF services to use gRPC helps provide the RPC features, performance, an interoperability needed in modern apps.</span></span>

* <span data-ttu-id="f7d82-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) è uno sforzo della community per fornire supporto per l'hosting di servizi WCF a .NET Core e .NET 5.</span><span class="sxs-lookup"><span data-stu-id="f7d82-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) is a community effort to bring support for hosting WCF services to .NET Core and .NET 5.</span></span> <span data-ttu-id="f7d82-157">È disponibile una versione di anteprima e il progetto sta lavorando per prepararsi alla produzione.</span><span class="sxs-lookup"><span data-stu-id="f7d82-157">A preview release is available and the project is working towards being production ready.</span></span> <span data-ttu-id="f7d82-158">CoreWCF supporta solo un subset delle funzionalità di WCF e .NET Framework le app di cui è stata eseguita la migrazione per usarle saranno necessarie modifiche al codice e test per avere esito positivo.</span><span class="sxs-lookup"><span data-stu-id="f7d82-158">CoreWCF only supports a subset of WCF's features, and .NET Framework apps that migrate to use it will need code changes and testing to be successful.</span></span> <span data-ttu-id="f7d82-159">CoreWCF è una scelta ottimale se un'applicazione deve mantenere la compatibilità con i client esistenti che chiamano i servizi WCF.</span><span class="sxs-lookup"><span data-stu-id="f7d82-159">CoreWCF is a good choice if an app has to maintain compatibility with existing clients that call WCF services.</span></span>

## <a name="get-started"></a><span data-ttu-id="f7d82-160">Introduzione</span><span class="sxs-lookup"><span data-stu-id="f7d82-160">Get started</span></span>

<span data-ttu-id="f7d82-161">Per istruzioni dettagliate sulla creazione di servizi gRPC in ASP.NET Core per gli sviluppatori WCF, vedere [ASP.NET Core gRPC per sviluppatori WCF](/dotnet/architecture/grpc-for-wcf-developers)</span><span class="sxs-lookup"><span data-stu-id="f7d82-161">For detailed guidance on building gRPC services in ASP.NET Core for WCF developers, see [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers)</span></span>
