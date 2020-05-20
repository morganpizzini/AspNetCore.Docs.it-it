---
<span data-ttu-id="8b716-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8b716-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b716-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b716-102">'Blazor'</span></span>
- <span data-ttu-id="8b716-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b716-103">'Identity'</span></span>
- <span data-ttu-id="8b716-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b716-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b716-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b716-105">'Razor'</span></span>
- <span data-ttu-id="8b716-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8b716-106">'SignalR' uid:</span></span> 

---
# <a name="grpc-services-with-c"></a><span data-ttu-id="8b716-107">Servizi gRPC con C\#</span><span class="sxs-lookup"><span data-stu-id="8b716-107">gRPC services with C\#</span></span>

<span data-ttu-id="8b716-108">Questo documento illustra i concetti necessari per scrivere app [gRPC](https://grpc.io/docs/guides/) in C#.</span><span class="sxs-lookup"><span data-stu-id="8b716-108">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="8b716-109">Gli argomenti trattati in questo articolo si applicano alle app gRPC basate su [C-core](https://grpc.io/blog/grpc-stacks)e su ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8b716-109">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a><span data-ttu-id="8b716-110">file proto</span><span class="sxs-lookup"><span data-stu-id="8b716-110">proto file</span></span>

<span data-ttu-id="8b716-111">gRPC usa un approccio basato sul contratto per lo sviluppo di API.</span><span class="sxs-lookup"><span data-stu-id="8b716-111">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="8b716-112">Per impostazione predefinita, i buffer di protocollo (protobuf) vengono usati come IDL (Interface Design Language).</span><span class="sxs-lookup"><span data-stu-id="8b716-112">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="8b716-113">Il file con \* \* estensione proto\* contiene:</span><span class="sxs-lookup"><span data-stu-id="8b716-113">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="8b716-114">Definizione del servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="8b716-114">The definition of the gRPC service.</span></span>
* <span data-ttu-id="8b716-115">Messaggi inviati tra client e server.</span><span class="sxs-lookup"><span data-stu-id="8b716-115">The messages sent between clients and servers.</span></span>

<span data-ttu-id="8b716-116">Per ulteriori informazioni sulla sintassi dei file protobuf, vedere la [documentazione ufficiale (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="8b716-116">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="8b716-117">Si consideri ad esempio il file *greet. proto* usato in [Introduzione al servizio gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="8b716-117">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="8b716-118">Definisce un `Greeter` servizio.</span><span class="sxs-lookup"><span data-stu-id="8b716-118">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="8b716-119">Il `Greeter` servizio definisce una `SayHello` chiamata.</span><span class="sxs-lookup"><span data-stu-id="8b716-119">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="8b716-120">`SayHello`Invia un `HelloRequest` messaggio e riceve un `HelloReply` messaggio:</span><span class="sxs-lookup"><span data-stu-id="8b716-120">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="8b716-121">Aggiungere un file. proto a un' \# app C</span><span class="sxs-lookup"><span data-stu-id="8b716-121">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="8b716-122">Il file con \* \* estensione proto\* è incluso in un progetto aggiungendolo al `<Protobuf>` gruppo di elementi:</span><span class="sxs-lookup"><span data-stu-id="8b716-122">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="8b716-123">Supporto degli strumenti C# per i file. proto</span><span class="sxs-lookup"><span data-stu-id="8b716-123">C# Tooling support for .proto files</span></span>

<span data-ttu-id="8b716-124">Il pacchetto di strumenti [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) è necessario per generare gli asset C# da file \* \* . proto\* .</span><span class="sxs-lookup"><span data-stu-id="8b716-124">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="8b716-125">Asset generati (file):</span><span class="sxs-lookup"><span data-stu-id="8b716-125">The generated assets (files):</span></span>

* <span data-ttu-id="8b716-126">Vengono generati ogni volta che il progetto viene compilato in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="8b716-126">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="8b716-127">Non vengono aggiunti al progetto o archiviati nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="8b716-127">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="8b716-128">È un artefatto di compilazione contenuto nella directory *obj* .</span><span class="sxs-lookup"><span data-stu-id="8b716-128">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="8b716-129">Questo pacchetto è necessario per i progetti server e client.</span><span class="sxs-lookup"><span data-stu-id="8b716-129">This package is required by both the server and client projects.</span></span> <span data-ttu-id="8b716-130">Il `Grpc.AspNetCore` metapacchetto include un riferimento a `Grpc.Tools` .</span><span class="sxs-lookup"><span data-stu-id="8b716-130">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="8b716-131">I progetti server possono `Grpc.AspNetCore` essere aggiunti tramite Gestione pacchetti in Visual Studio o aggiungendo un `<PackageReference>` al file di progetto:</span><span class="sxs-lookup"><span data-stu-id="8b716-131">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="8b716-132">I progetti client devono fare riferimento direttamente `Grpc.Tools` a insieme agli altri pacchetti necessari per usare il client gRPC.</span><span class="sxs-lookup"><span data-stu-id="8b716-132">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="8b716-133">Il pacchetto di strumenti non è necessario in fase di esecuzione, quindi la dipendenza è contrassegnata con `PrivateAssets="All"` :</span><span class="sxs-lookup"><span data-stu-id="8b716-133">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="8b716-134">Asset C# generati</span><span class="sxs-lookup"><span data-stu-id="8b716-134">Generated C# assets</span></span>

<span data-ttu-id="8b716-135">Il pacchetto di strumenti genera i tipi C# che rappresentano i messaggi definiti nei file con \* \* estensione proto\* inclusi.</span><span class="sxs-lookup"><span data-stu-id="8b716-135">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="8b716-136">Per gli asset lato server, viene generato un tipo di base del servizio abstract.</span><span class="sxs-lookup"><span data-stu-id="8b716-136">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="8b716-137">Il tipo di base contiene le definizioni di tutte le chiamate gRPC contenute nel file *. proto* .</span><span class="sxs-lookup"><span data-stu-id="8b716-137">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="8b716-138">Creare un'implementazione del servizio concreta che deriva da questo tipo di base e implementa la logica per le chiamate gRPC.</span><span class="sxs-lookup"><span data-stu-id="8b716-138">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="8b716-139">Per `greet.proto` , l'esempio descritto in precedenza, `GreeterBase` viene generato un tipo astratto che contiene un `SayHello` metodo virtuale.</span><span class="sxs-lookup"><span data-stu-id="8b716-139">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="8b716-140">Un'implementazione concreta `GreeterService` esegue l'override del metodo e implementa la logica di gestione della chiamata gRPC.</span><span class="sxs-lookup"><span data-stu-id="8b716-140">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="8b716-141">Per gli asset lato client viene generato un tipo di client concreto.</span><span class="sxs-lookup"><span data-stu-id="8b716-141">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="8b716-142">Le chiamate gRPC nel file *. proto* vengono convertite in metodi sul tipo concreto, che può essere chiamato.</span><span class="sxs-lookup"><span data-stu-id="8b716-142">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="8b716-143">Per l' `greet.proto` esempio descritto in precedenza, `GreeterClient` viene generato un tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="8b716-143">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="8b716-144">Chiamare `GreeterClient.SayHelloAsync` per avviare una chiamata gRPC al server.</span><span class="sxs-lookup"><span data-stu-id="8b716-144">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="8b716-145">Per impostazione predefinita, gli asset server e client vengono generati per ogni file con \* \* estensione proto\* incluso nel `<Protobuf>` gruppo di elementi.</span><span class="sxs-lookup"><span data-stu-id="8b716-145">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="8b716-146">Per assicurarsi che vengano generati solo gli asset server in un progetto server, l' `GrpcServices` attributo è impostato su `Server` .</span><span class="sxs-lookup"><span data-stu-id="8b716-146">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="8b716-147">Analogamente, l'attributo viene impostato su `Client` nei progetti client.</span><span class="sxs-lookup"><span data-stu-id="8b716-147">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b716-148">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8b716-148">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
