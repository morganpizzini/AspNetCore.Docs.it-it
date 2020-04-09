---
title: Risolvere i problemi relativi a gRPC in .NET Core
author: jamesnk
description: Risolvere gli errori quando si usa gRPC in .NET Core.Troubleshoot errors when using gRPC on .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664129"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="87737-103">Risolvere i problemi relativi a gRPC in .NET Core</span><span class="sxs-lookup"><span data-stu-id="87737-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="87737-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="87737-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="87737-105">In questo documento vengono illustrati i problemi comuni durante lo sviluppo di app gRPC in .NET.</span><span class="sxs-lookup"><span data-stu-id="87737-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="87737-106">Mancata corrispondenza tra configurazione SSL/TLS del client e del servizio</span><span class="sxs-lookup"><span data-stu-id="87737-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="87737-107">Il modello gRPC e gli esempi [utilizzano Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) per proteggere i servizi gRPC per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="87737-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="87737-108">I client gRPC devono utilizzare una connessione protetta per chiamare correttamente i servizi gRPC protetti.</span><span class="sxs-lookup"><span data-stu-id="87737-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="87737-109">È possibile verificare che il servizio gRPC di ASP.NET tramite utilizzi TLS nei registri scritti all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="87737-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="87737-110">Il servizio sarà in ascolto su un endpoint HTTPS:The service will be listening on an HTTPS endpoint:</span><span class="sxs-lookup"><span data-stu-id="87737-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="87737-111">Il client .NET `https` Core deve utilizzare nell'indirizzo del server per effettuare chiamate con una connessione protetta:The .NET Core client must use in the server address to make calls with a secured connection:</span><span class="sxs-lookup"><span data-stu-id="87737-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="87737-112">Tutte le implementazioni client gRPC supportano TLS.</span><span class="sxs-lookup"><span data-stu-id="87737-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="87737-113">I client gRPC di altre lingue `SslCredentials`richiedono in genere il canale configurato con .</span><span class="sxs-lookup"><span data-stu-id="87737-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="87737-114">`SslCredentials`specifica il certificato che verrà utilizzato dal client e deve essere utilizzato al posto delle credenziali non sicure.</span><span class="sxs-lookup"><span data-stu-id="87737-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="87737-115">Per esempi di configurazione delle diverse implementazioni client gRPC per l'utilizzo di TLS, vedere [Autenticazione gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="87737-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="87737-116">Chiamare un servizio gRPC con un certificato non attendibile/non validoCall a gRPC service with an untrusted/invalid certificate</span><span class="sxs-lookup"><span data-stu-id="87737-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="87737-117">Il client gRPC .NET richiede che il servizio disponga di un certificato attendibile.</span><span class="sxs-lookup"><span data-stu-id="87737-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="87737-118">Quando si chiama un servizio gRPC senza un certificato attendibile, viene restituito il seguente messaggio di errore:</span><span class="sxs-lookup"><span data-stu-id="87737-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="87737-119">Eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="87737-119">Unhandled exception.</span></span> <span data-ttu-id="87737-120">System.Net.Http.HttpRequestException: Impossibile stabilire la connessione SSL, vedere eccezione interna.</span><span class="sxs-lookup"><span data-stu-id="87737-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="87737-121">---> System.Security.Authentication.AuthenticationException: Il certificato remoto non è stato ritenuto valido dalla procedura di convalida.</span><span class="sxs-lookup"><span data-stu-id="87737-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="87737-122">Questo errore può essere visualizzato se si sta testando l'app in locale e il certificato di sviluppo HTTPS di ASP.NET core non è attendibile.</span><span class="sxs-lookup"><span data-stu-id="87737-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="87737-123">Per istruzioni su come risolvere questo problema, vedere [Considerare attendibile il certificato di sviluppo di ASP.NET Core HTTPS in Windows e macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="87737-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="87737-124">Se si chiama un servizio gRPC su un altro computer e non si riesce a considerare attendibile il certificato, il client gRPC può essere configurato per ignorare il certificato non valido.</span><span class="sxs-lookup"><span data-stu-id="87737-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="87737-125">Il codice seguente usa HttpClientHandler.ServerCertificateCustomValidationCallback per consentire chiamate senza un certificato attendibile:The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span><span class="sxs-lookup"><span data-stu-id="87737-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="87737-126">I certificati non attendibili devono essere usati solo durante lo sviluppo dell'app.</span><span class="sxs-lookup"><span data-stu-id="87737-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="87737-127">Le app di produzione devono sempre usare certificati validi.</span><span class="sxs-lookup"><span data-stu-id="87737-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="87737-128">Chiamare servizi gRPC non sicuri con il client .NET CoreCall insecure gRPC services with .NET Core client</span><span class="sxs-lookup"><span data-stu-id="87737-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="87737-129">È necessaria una configurazione aggiuntiva per chiamare servizi gRPC non sicuri con il client .NET Core.</span><span class="sxs-lookup"><span data-stu-id="87737-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="87737-130">Il client gRPC `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` deve `true` impostare `http` l'opzione su e utilizzarla nell'indirizzo del server:</span><span class="sxs-lookup"><span data-stu-id="87737-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="87737-131">Impossibile avviare ASP.NET'app gRPC core su macOS</span><span class="sxs-lookup"><span data-stu-id="87737-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="87737-132">Kestrel non supporta HTTP/2 con TLS su macOS e versioni precedenti di Windows come Windows 7.</span><span class="sxs-lookup"><span data-stu-id="87737-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="87737-133">Il modello gRPC ASP.NET Core e gli esempi utilizzano TLS per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="87737-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="87737-134">Quando si tenta di avviare il server gRPC, verrà visualizzato il seguente messaggio di errore:</span><span class="sxs-lookup"><span data-stu-id="87737-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="87737-135">Impossibile eseguire https://localhost:5001 l'associazione nell'interfaccia di loopback IPv4: 'HTTP/2 su TLS non è supportato in macOS a causa del supporto ALPN mancante'.</span><span class="sxs-lookup"><span data-stu-id="87737-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="87737-136">Per risolvere questo problema, configurare Kestrel e il client gRPC per utilizzare HTTP/2 *senza* TLS.</span><span class="sxs-lookup"><span data-stu-id="87737-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="87737-137">È consigliabile eseguire questa operazione solo durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="87737-137">You should only do this during development.</span></span> <span data-ttu-id="87737-138">Se non si utilizza TLS, i messaggi gRPC verranno inviati senza crittografia.</span><span class="sxs-lookup"><span data-stu-id="87737-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="87737-139">Kestrel deve configurare un endpoint HTTP/2 senza TLS in *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="87737-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="87737-140">Quando un endpoint HTTP/2 è configurato senza TLS, [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) dell'endpoint deve essere impostato su `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="87737-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="87737-141">`HttpProtocols.Http1AndHttp2`non può essere utilizzato perché TLS è necessario per negoziare HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="87737-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="87737-142">Senza TLS, tutte le connessioni all'endpoint vengono tutte predefinite a HTTP/1.1 e le chiamate gRPC hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="87737-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="87737-143">Il client gRPC deve inoltre essere configurato per non utilizzare TLS.</span><span class="sxs-lookup"><span data-stu-id="87737-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="87737-144">Per ulteriori informazioni, consultate [Chiamare servizi gRPC non sicuri con il client .NET Core.](#call-insecure-grpc-services-with-net-core-client)</span><span class="sxs-lookup"><span data-stu-id="87737-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="87737-145">HTTP/2 senza TLS deve essere usato solo durante lo sviluppo dell'app.</span><span class="sxs-lookup"><span data-stu-id="87737-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="87737-146">Le app di produzione devono sempre usare la sicurezza del trasporto.</span><span class="sxs-lookup"><span data-stu-id="87737-146">Production apps should always use transport security.</span></span> <span data-ttu-id="87737-147">Per ulteriori informazioni, vedere [Considerazioni sulla sicurezza in gRPC per ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="87737-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="87737-148">Le risorse di gRPC di C</span><span class="sxs-lookup"><span data-stu-id="87737-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="87737-149">La generazione di codice gRPC di client concreti e classi di base del servizio richiede l'invio di file e strumenti protobuf da un progetto.</span><span class="sxs-lookup"><span data-stu-id="87737-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="87737-150">È necessario includere:</span><span class="sxs-lookup"><span data-stu-id="87737-150">You must include:</span></span>

* <span data-ttu-id="87737-151">*.proto* che si desidera `<Protobuf>` utilizzare nel gruppo di elementi.</span><span class="sxs-lookup"><span data-stu-id="87737-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="87737-152">[I file *.proto* importati](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) devono fare riferimento al progetto.</span><span class="sxs-lookup"><span data-stu-id="87737-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="87737-153">Riferimento del pacchetto al pacchetto di strumenti gRPC [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="87737-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="87737-154">Per ulteriori informazioni sulla generazione di asset <xref:grpc/basics>gRPC in C, vedere .</span><span class="sxs-lookup"><span data-stu-id="87737-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="87737-155">Per impostazione `<Protobuf>` predefinita, un riferimento genera un client concreto e una classe base del servizio.</span><span class="sxs-lookup"><span data-stu-id="87737-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="87737-156">L'attributo `GrpcServices` dell'elemento di riferimento può essere utilizzato per limitare la generazione di asset di C.</span><span class="sxs-lookup"><span data-stu-id="87737-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="87737-157">Le `GrpcServices` opzioni valide sono:</span><span class="sxs-lookup"><span data-stu-id="87737-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="87737-158">`Both`(impostazione predefinita quando non presente)</span><span class="sxs-lookup"><span data-stu-id="87737-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="87737-159">Un'app Web ASP.NET Core che ospita i servizi gRPC richiede solo la classe di base del servizio generata:</span><span class="sxs-lookup"><span data-stu-id="87737-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="87737-160">Un'applicazione client gRPC che effettua chiamate gRPC richiede solo il client concreto generato:</span><span class="sxs-lookup"><span data-stu-id="87737-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="87737-161">Progetti WPF in grado di generare risorse gRPC di C</span><span class="sxs-lookup"><span data-stu-id="87737-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="87737-162">I progetti WPFWPF presentano un [problema noto](https://github.com/dotnet/wpf/issues/810) che impedisce il corretto funzionamento della generazione di codice gRPC.</span><span class="sxs-lookup"><span data-stu-id="87737-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="87737-163">Qualsiasi tipo gRPC generato in un `Grpc.Tools` progetto WPF facendo riferimento ai file *.proto* creerà errori di compilazione quando utilizzati:</span><span class="sxs-lookup"><span data-stu-id="87737-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="87737-164">Errore CS0246: Impossibile trovare il nome del tipo o dello spazio dei nomi 'MyGrpcServices' (manca una direttiva using o un riferimento all'assembly?)</span><span class="sxs-lookup"><span data-stu-id="87737-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="87737-165">È possibile risolvere questo problema:</span><span class="sxs-lookup"><span data-stu-id="87737-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="87737-166">Creare un nuovo progetto di libreria di classi .NET Core.Create a new .NET Core class library project.</span><span class="sxs-lookup"><span data-stu-id="87737-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="87737-167">Nel nuovo progetto, aggiungere i riferimenti per consentire la generazione di [codice C , dai \* \*\* file con estensione proto](xref:grpc/basics#generated-c-assets):</span><span class="sxs-lookup"><span data-stu-id="87737-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="87737-168">Aggiungere un riferimento al pacchetto [Grpc.Tools.Add](https://www.nuget.org/packages/Grpc.Tools/) a package reference to Grpc.Tools package.</span><span class="sxs-lookup"><span data-stu-id="87737-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="87737-169">Aggiungere \* \*\* file con estensione `<Protobuf>` proto al gruppo di elementi.</span><span class="sxs-lookup"><span data-stu-id="87737-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="87737-170">Nell'applicazione WPFWPF aggiungere un riferimento al nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="87737-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="87737-171">L'applicazione WPFWPF può usare i tipi generati gRPC dal nuovo progetto di libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="87737-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
