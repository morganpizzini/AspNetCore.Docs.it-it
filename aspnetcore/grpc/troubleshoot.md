---
title: Risolvere i problemi di gRPC in .NET Core
author: jamesnk
description: Risolvere gli errori quando si usa gRPC in .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/troubleshoot
ms.openlocfilehash: a366da7b6a12f6cd10af1abcb7ca76cf8e367277
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016011"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="28b94-103">Risolvere i problemi di gRPC in .NET Core</span><span class="sxs-lookup"><span data-stu-id="28b94-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="28b94-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="28b94-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="28b94-105">Questo documento illustra i problemi comuni riscontrati durante lo sviluppo di app gRPC in .NET.</span><span class="sxs-lookup"><span data-stu-id="28b94-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="28b94-106">Mancata corrispondenza tra la configurazione SSL/TLS del client e del servizio</span><span class="sxs-lookup"><span data-stu-id="28b94-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="28b94-107">Il modello e gli esempi di gRPC usano [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) per proteggere i servizi gRPC per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="28b94-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="28b94-108">i client gRPC devono usare una connessione sicura per chiamare correttamente i servizi gRPC protetti.</span><span class="sxs-lookup"><span data-stu-id="28b94-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="28b94-109">È possibile verificare che ASP.NET Core servizio gRPC stia usando TLS nei log scritti all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="28b94-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="28b94-110">Il servizio sarà in ascolto su un endpoint HTTPS:</span><span class="sxs-lookup"><span data-stu-id="28b94-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="28b94-111">Il client .NET Core deve usare `https` nell'indirizzo del server per effettuare chiamate con una connessione protetta:</span><span class="sxs-lookup"><span data-stu-id="28b94-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="28b94-112">Tutte le implementazioni client di gRPC supportano TLS.</span><span class="sxs-lookup"><span data-stu-id="28b94-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="28b94-113">i client gRPC di altri linguaggi richiedono in genere il canale configurato con `SslCredentials` .</span><span class="sxs-lookup"><span data-stu-id="28b94-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="28b94-114">`SslCredentials`Specifica il certificato che verrà utilizzato dal client e deve essere utilizzato al posto di credenziali non sicure.</span><span class="sxs-lookup"><span data-stu-id="28b94-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="28b94-115">Per esempi di configurazione delle diverse implementazioni client di gRPC per l'uso di TLS, vedere [autenticazione gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="28b94-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="28b94-116">Chiamare un servizio gRPC con un certificato non attendibile/non valido</span><span class="sxs-lookup"><span data-stu-id="28b94-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="28b94-117">Il client gRPC .NET richiede che il servizio disponga di un certificato attendibile.</span><span class="sxs-lookup"><span data-stu-id="28b94-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="28b94-118">Quando si chiama un servizio gRPC senza un certificato attendibile, viene restituito il messaggio di errore seguente:</span><span class="sxs-lookup"><span data-stu-id="28b94-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="28b94-119">Eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="28b94-119">Unhandled exception.</span></span> <span data-ttu-id="28b94-120">System .NET. http. HttpRequestexception: non è stato possibile stabilire la connessione SSL. vedere l'eccezione interna.</span><span class="sxs-lookup"><span data-stu-id="28b94-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="28b94-121">---> System.Security.Authentication.AuthenticationException: Il certificato remoto non è stato ritenuto valido dalla procedura di convalida.</span><span class="sxs-lookup"><span data-stu-id="28b94-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="28b94-122">Questo errore può essere visualizzato se si sta testando l'app in locale e il certificato di sviluppo HTTPS ASP.NET Core non è attendibile.</span><span class="sxs-lookup"><span data-stu-id="28b94-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="28b94-123">Per istruzioni su come risolvere questo problema, vedere [Considerare attendibile il certificato di sviluppo di ASP.NET Core HTTPS in Windows e macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="28b94-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="28b94-124">Se si chiama un servizio gRPC in un altro computer e non è possibile considerare attendibile il certificato, il client gRPC può essere configurato in modo da ignorare il certificato non valido.</span><span class="sxs-lookup"><span data-stu-id="28b94-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="28b94-125">Il codice seguente usa [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) per consentire le chiamate senza un certificato attendibile:</span><span class="sxs-lookup"><span data-stu-id="28b94-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="28b94-126">I certificati non attendibili devono essere usati solo durante lo sviluppo di app.</span><span class="sxs-lookup"><span data-stu-id="28b94-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="28b94-127">Le app di produzione devono sempre usare certificati validi.</span><span class="sxs-lookup"><span data-stu-id="28b94-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="28b94-128">Chiamare i servizi gRPC non sicuri con il client .NET Core</span><span class="sxs-lookup"><span data-stu-id="28b94-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="28b94-129">È necessaria una configurazione aggiuntiva per chiamare i servizi gRPC non protetti con il client .NET Core.</span><span class="sxs-lookup"><span data-stu-id="28b94-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="28b94-130">Il client gRPC deve impostare l' `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` opzione su `true` e usare `http` nell'indirizzo del server:</span><span class="sxs-lookup"><span data-stu-id="28b94-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="28b94-131">Non è possibile avviare ASP.NET Core app gRPC in macOS</span><span class="sxs-lookup"><span data-stu-id="28b94-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="28b94-132">Gheppio non supporta HTTP/2 con TLS in macOS e versioni precedenti di Windows, ad esempio Windows 7.</span><span class="sxs-lookup"><span data-stu-id="28b94-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="28b94-133">Per impostazione predefinita, il ASP.NET Core modello e gli esempi di gRPC usano TLS.</span><span class="sxs-lookup"><span data-stu-id="28b94-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="28b94-134">Quando si tenta di avviare il server gRPC, viene visualizzato il messaggio di errore seguente:</span><span class="sxs-lookup"><span data-stu-id="28b94-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="28b94-135">Non è possibile eseguire l'associazione a nell' https://localhost:5001 interfaccia di loopback IPv4:' http/2 su TLS non è supportato in MacOS perché manca il supporto di ALPN '.</span><span class="sxs-lookup"><span data-stu-id="28b94-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="28b94-136">Per risolvere questo problema, configurare gheppio e il client gRPC per l'uso di HTTP/2 *senza* TLS.</span><span class="sxs-lookup"><span data-stu-id="28b94-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="28b94-137">Questa operazione deve essere eseguita solo durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="28b94-137">You should only do this during development.</span></span> <span data-ttu-id="28b94-138">Se non si usa TLS, i messaggi gRPC vengono inviati senza crittografia.</span><span class="sxs-lookup"><span data-stu-id="28b94-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="28b94-139">Gheppio deve configurare un endpoint HTTP/2 senza TLS in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="28b94-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="28b94-140">Quando un endpoint HTTP/2 viene configurato senza TLS, l'endpoint [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) deve essere impostato su `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="28b94-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="28b94-141">`HttpProtocols.Http1AndHttp2`non può essere usato perché TLS è necessario per negoziare HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="28b94-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="28b94-142">Senza TLS, tutte le connessioni all'endpoint vengono predefinite a HTTP/1.1 e le chiamate a gRPC hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="28b94-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="28b94-143">Il client di gRPC deve essere configurato anche per non usare TLS.</span><span class="sxs-lookup"><span data-stu-id="28b94-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="28b94-144">Per altre informazioni, vedere [chiamare i servizi gRPC non sicuri con il client .NET Core](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="28b94-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="28b94-145">HTTP/2 senza TLS deve essere usato solo durante lo sviluppo di app.</span><span class="sxs-lookup"><span data-stu-id="28b94-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="28b94-146">Le app di produzione devono sempre usare la sicurezza del trasporto.</span><span class="sxs-lookup"><span data-stu-id="28b94-146">Production apps should always use transport security.</span></span> <span data-ttu-id="28b94-147">Per ulteriori informazioni, vedere [considerazioni sulla sicurezza in gRPC per ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="28b94-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="28b94-148">gli asset C# di gRPC non sono generati da file con estensione proto</span><span class="sxs-lookup"><span data-stu-id="28b94-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="28b94-149">per la generazione di codice gRPC di client concreti e classi di base del servizio è necessario fare riferimento a file e strumenti protobuf da un progetto.</span><span class="sxs-lookup"><span data-stu-id="28b94-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="28b94-150">È necessario includere:</span><span class="sxs-lookup"><span data-stu-id="28b94-150">You must include:</span></span>

* <span data-ttu-id="28b94-151">file con *estensione proto* che si desidera utilizzare nel `<Protobuf>` gruppo di elementi.</span><span class="sxs-lookup"><span data-stu-id="28b94-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="28b94-152">Il progetto deve fare riferimento ai [file *. proto* importati](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) .</span><span class="sxs-lookup"><span data-stu-id="28b94-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="28b94-153">Riferimento al pacchetto per il pacchetto di [strumenti GRPC gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="28b94-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="28b94-154">Per ulteriori informazioni sulla generazione di asset gRPC C#, vedere <xref:grpc/basics> .</span><span class="sxs-lookup"><span data-stu-id="28b94-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="28b94-155">Un ASP.NET Core app Web che ospita i servizi gRPC richiede solo la classe di base del servizio generata:</span><span class="sxs-lookup"><span data-stu-id="28b94-155">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="28b94-156">Un'app client gRPC che effettua chiamate gRPC richiede solo il client concreto generato:</span><span class="sxs-lookup"><span data-stu-id="28b94-156">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="28b94-157">I progetti WPF non sono in grado di generare asset C# gRPC da file. proto</span><span class="sxs-lookup"><span data-stu-id="28b94-157">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="28b94-158">I progetti WPF presentano un [problema noto](https://github.com/dotnet/wpf/issues/810) che impedisce il corretto funzionamento della generazione del codice gRPC.</span><span class="sxs-lookup"><span data-stu-id="28b94-158">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="28b94-159">Tutti i tipi di gRPC generati in un progetto WPF facendo riferimento ai `Grpc.Tools` file *. proto e. proto* creeranno errori di compilazione quando vengono utilizzati:</span><span class="sxs-lookup"><span data-stu-id="28b94-159">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="28b94-160">errore CS0246: Impossibile trovare il tipo o il nome dello spazio dei nomi "MyGrpcServices". manca una direttiva using o un riferimento a un assembly.</span><span class="sxs-lookup"><span data-stu-id="28b94-160">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="28b94-161">Per aggirare questo problema, è possibile:</span><span class="sxs-lookup"><span data-stu-id="28b94-161">You can workaround this issue by:</span></span>

1. <span data-ttu-id="28b94-162">Creare un nuovo progetto libreria di classi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="28b94-162">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="28b94-163">Nel nuovo progetto aggiungere i riferimenti per abilitare la [generazione di codice C# da file \* \* . proto\* ](xref:grpc/basics#generated-c-assets):</span><span class="sxs-lookup"><span data-stu-id="28b94-163">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="28b94-164">Aggiungere un riferimento al pacchetto [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="28b94-164">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="28b94-165">Aggiungere i file \* \* . proto\* al `<Protobuf>` gruppo di elementi.</span><span class="sxs-lookup"><span data-stu-id="28b94-165">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="28b94-166">Nell'applicazione WPF aggiungere un riferimento al nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="28b94-166">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="28b94-167">L'applicazione WPF può utilizzare i tipi generati da gRPC dal nuovo progetto libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="28b94-167">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
