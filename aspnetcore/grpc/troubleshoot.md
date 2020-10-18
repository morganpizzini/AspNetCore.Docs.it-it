---
title: Risolvere i problemi di gRPC in .NET Core
author: jamesnk
description: Risolvere gli errori quando si usa gRPC in .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/09/2020
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
uid: grpc/troubleshoot
ms.openlocfilehash: 0c897c8c640f8713fc7d3b6cad0e6c571131d7a5
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113842"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="fcb9b-103">Risolvere i problemi di gRPC in .NET Core</span><span class="sxs-lookup"><span data-stu-id="fcb9b-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="fcb9b-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="fcb9b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="fcb9b-105">Questo documento illustra i problemi comuni riscontrati durante lo sviluppo di app gRPC in .NET.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="fcb9b-106">Mancata corrispondenza tra la configurazione SSL/TLS del client e del servizio</span><span class="sxs-lookup"><span data-stu-id="fcb9b-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="fcb9b-107">Il modello e gli esempi di gRPC usano [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) per proteggere i servizi gRPC per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="fcb9b-108">i client gRPC devono usare una connessione sicura per chiamare correttamente i servizi gRPC protetti.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="fcb9b-109">È possibile verificare che ASP.NET Core servizio gRPC stia usando TLS nei log scritti all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="fcb9b-110">Il servizio sarà in ascolto su un endpoint HTTPS:</span><span class="sxs-lookup"><span data-stu-id="fcb9b-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="fcb9b-111">Il client .NET Core deve usare `https` nell'indirizzo del server per effettuare chiamate con una connessione protetta:</span><span class="sxs-lookup"><span data-stu-id="fcb9b-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="fcb9b-112">Tutte le implementazioni client di gRPC supportano TLS.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="fcb9b-113">i client gRPC di altri linguaggi richiedono in genere il canale configurato con `SslCredentials` .</span><span class="sxs-lookup"><span data-stu-id="fcb9b-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="fcb9b-114">`SslCredentials` Specifica il certificato che verrà utilizzato dal client e deve essere utilizzato al posto di credenziali non sicure.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="fcb9b-115">Per esempi di configurazione delle diverse implementazioni client di gRPC per l'uso di TLS, vedere [autenticazione gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="fcb9b-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="fcb9b-116">Chiamare un servizio gRPC con un certificato non attendibile/non valido</span><span class="sxs-lookup"><span data-stu-id="fcb9b-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="fcb9b-117">Il client gRPC .NET richiede che il servizio disponga di un certificato attendibile.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="fcb9b-118">Quando si chiama un servizio gRPC senza un certificato attendibile, viene restituito il messaggio di errore seguente:</span><span class="sxs-lookup"><span data-stu-id="fcb9b-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="fcb9b-119">Eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-119">Unhandled exception.</span></span> <span data-ttu-id="fcb9b-120">System .NET. http. HttpRequestexception: non è stato possibile stabilire la connessione SSL. vedere l'eccezione interna.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="fcb9b-121">---> System.Security.Authentication.AuthenticationException: Il certificato remoto non è stato ritenuto valido dalla procedura di convalida.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="fcb9b-122">Questo errore può essere visualizzato se si sta testando l'app in locale e il certificato di sviluppo HTTPS ASP.NET Core non è attendibile.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="fcb9b-123">Per istruzioni su come risolvere questo problema, vedere [Considerare attendibile il certificato di sviluppo di ASP.NET Core HTTPS in Windows e macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="fcb9b-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="fcb9b-124">Se si chiama un servizio gRPC in un altro computer e non è possibile considerare attendibile il certificato, il client gRPC può essere configurato in modo da ignorare il certificato non valido.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="fcb9b-125">Il codice seguente usa [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) per consentire le chiamate senza un certificato attendibile:</span><span class="sxs-lookup"><span data-stu-id="fcb9b-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

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
> <span data-ttu-id="fcb9b-126">I certificati non attendibili devono essere usati solo durante lo sviluppo di app.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="fcb9b-127">Le app di produzione devono sempre usare certificati validi.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="fcb9b-128">Chiamare i servizi gRPC non sicuri con il client .NET Core</span><span class="sxs-lookup"><span data-stu-id="fcb9b-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="fcb9b-129">Quando un'app usa .NET Core 3. x, è necessaria una configurazione aggiuntiva per chiamare i servizi gRPC non protetti con il client .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-129">When an app is using .NET Core 3.x, additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="fcb9b-130">Il client gRPC deve impostare l' `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` opzione su `true` e usare `http` nell'indirizzo del server:</span><span class="sxs-lookup"><span data-stu-id="fcb9b-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="fcb9b-131">Per le app .NET 5 non sono necessarie configurazioni aggiuntive, ma per chiamare i servizi gRPC non sicuri è necessario usare la `Grpc.Net.Client` versione 2.32.0 o successiva.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-131">.NET 5 apps don't need additional configuration, but to call insecure gRPC services they must use `Grpc.Net.Client` version 2.32.0 or later.</span></span>

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="fcb9b-132">Non è possibile avviare ASP.NET Core app gRPC in macOS</span><span class="sxs-lookup"><span data-stu-id="fcb9b-132">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="fcb9b-133">Gheppio non supporta HTTP/2 con TLS in macOS e versioni precedenti di Windows, ad esempio Windows 7.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-133">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="fcb9b-134">Per impostazione predefinita, il ASP.NET Core modello e gli esempi di gRPC usano TLS.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-134">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="fcb9b-135">Quando si tenta di avviare il server gRPC, viene visualizzato il messaggio di errore seguente:</span><span class="sxs-lookup"><span data-stu-id="fcb9b-135">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="fcb9b-136">Non è possibile eseguire l'associazione a nell' https://localhost:5001 interfaccia di loopback IPv4:' http/2 su TLS non è supportato in MacOS perché manca il supporto di ALPN '.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-136">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="fcb9b-137">Per risolvere questo problema, configurare gheppio e il client gRPC per l'uso di HTTP/2 *senza* TLS.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-137">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="fcb9b-138">Questa operazione deve essere eseguita solo durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-138">You should only do this during development.</span></span> <span data-ttu-id="fcb9b-139">Se non si usa TLS, i messaggi gRPC vengono inviati senza crittografia.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-139">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="fcb9b-140">Gheppio deve configurare un endpoint HTTP/2 senza TLS in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fcb9b-140">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="fcb9b-141">Quando un endpoint HTTP/2 viene configurato senza TLS, l'endpoint [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) deve essere impostato su `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="fcb9b-141">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="fcb9b-142">`HttpProtocols.Http1AndHttp2` non può essere usato perché TLS è necessario per negoziare HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-142">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="fcb9b-143">Senza TLS, tutte le connessioni all'endpoint vengono predefinite a HTTP/1.1 e le chiamate a gRPC hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-143">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="fcb9b-144">Il client di gRPC deve essere configurato anche per non usare TLS.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-144">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="fcb9b-145">Per altre informazioni, vedere [chiamare i servizi gRPC non sicuri con il client .NET Core](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="fcb9b-145">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="fcb9b-146">HTTP/2 senza TLS deve essere usato solo durante lo sviluppo di app.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-146">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="fcb9b-147">Le app di produzione devono sempre usare la sicurezza del trasporto.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-147">Production apps should always use transport security.</span></span> <span data-ttu-id="fcb9b-148">Per ulteriori informazioni, vedere [considerazioni sulla sicurezza in gRPC per ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="fcb9b-148">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="fcb9b-149">gli asset C# di gRPC non sono generati da file con estensione proto</span><span class="sxs-lookup"><span data-stu-id="fcb9b-149">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="fcb9b-150">per la generazione di codice gRPC di client concreti e classi di base del servizio è necessario fare riferimento a file e strumenti protobuf da un progetto.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-150">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="fcb9b-151">È necessario includere:</span><span class="sxs-lookup"><span data-stu-id="fcb9b-151">You must include:</span></span>

* <span data-ttu-id="fcb9b-152">file con *estensione proto* che si desidera utilizzare nel `<Protobuf>` gruppo di elementi.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-152">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="fcb9b-153">Il progetto deve fare riferimento ai [file *. proto* importati](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) .</span><span class="sxs-lookup"><span data-stu-id="fcb9b-153">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="fcb9b-154">Riferimento al pacchetto per il pacchetto di [strumenti GRPC gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="fcb9b-154">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="fcb9b-155">Per ulteriori informazioni sulla generazione di asset gRPC C#, vedere <xref:grpc/basics> .</span><span class="sxs-lookup"><span data-stu-id="fcb9b-155">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="fcb9b-156">Un ASP.NET Core app Web che ospita i servizi gRPC richiede solo la classe di base del servizio generata:</span><span class="sxs-lookup"><span data-stu-id="fcb9b-156">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="fcb9b-157">Un'app client gRPC che effettua chiamate gRPC richiede solo il client concreto generato:</span><span class="sxs-lookup"><span data-stu-id="fcb9b-157">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="fcb9b-158">I progetti WPF non sono in grado di generare asset C# gRPC da file. proto</span><span class="sxs-lookup"><span data-stu-id="fcb9b-158">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="fcb9b-159">I progetti WPF presentano un [problema noto](https://github.com/dotnet/wpf/issues/810) che impedisce il corretto funzionamento della generazione del codice gRPC.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-159">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="fcb9b-160">Tutti i tipi di gRPC generati in un progetto WPF facendo riferimento ai `Grpc.Tools` file *. proto e. proto* creeranno errori di compilazione quando vengono utilizzati:</span><span class="sxs-lookup"><span data-stu-id="fcb9b-160">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="fcb9b-161">errore CS0246: Impossibile trovare il tipo o il nome dello spazio dei nomi "MyGrpcServices". manca una direttiva using o un riferimento a un assembly.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-161">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="fcb9b-162">Per aggirare questo problema, è possibile:</span><span class="sxs-lookup"><span data-stu-id="fcb9b-162">You can workaround this issue by:</span></span>

1. <span data-ttu-id="fcb9b-163">Creare un nuovo progetto libreria di classi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-163">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="fcb9b-164">Nel nuovo progetto aggiungere i riferimenti per abilitare la [generazione di codice C# da file \* \* . proto\* ](xref:grpc/basics#generated-c-assets):</span><span class="sxs-lookup"><span data-stu-id="fcb9b-164">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="fcb9b-165">Aggiungere un riferimento al pacchetto [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="fcb9b-165">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="fcb9b-166">Aggiungere i file \* \* . proto\* al `<Protobuf>` gruppo di elementi.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-166">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="fcb9b-167">Nell'applicazione WPF aggiungere un riferimento al nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-167">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="fcb9b-168">L'applicazione WPF può utilizzare i tipi generati da gRPC dal nuovo progetto libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="fcb9b-168">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
