---
<span data-ttu-id="7805d-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="7805d-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7805d-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7805d-102">'Blazor'</span></span>
- <span data-ttu-id="7805d-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7805d-103">'Identity'</span></span>
- <span data-ttu-id="7805d-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7805d-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="7805d-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7805d-105">'Razor'</span></span>
- <span data-ttu-id="7805d-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="7805d-106">'SignalR' uid:</span></span> 

---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="7805d-107">Risolvere i problemi di gRPC in .NET Core</span><span class="sxs-lookup"><span data-stu-id="7805d-107">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="7805d-108">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="7805d-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="7805d-109">Questo documento illustra i problemi comuni riscontrati durante lo sviluppo di app gRPC in .NET.</span><span class="sxs-lookup"><span data-stu-id="7805d-109">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="7805d-110">Mancata corrispondenza tra la configurazione SSL/TLS del client e del servizio</span><span class="sxs-lookup"><span data-stu-id="7805d-110">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="7805d-111">Il modello e gli esempi di gRPC usano [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) per proteggere i servizi gRPC per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="7805d-111">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="7805d-112">i client gRPC devono usare una connessione sicura per chiamare correttamente i servizi gRPC protetti.</span><span class="sxs-lookup"><span data-stu-id="7805d-112">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="7805d-113">È possibile verificare che ASP.NET Core servizio gRPC stia usando TLS nei log scritti all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="7805d-113">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="7805d-114">Il servizio sarà in ascolto su un endpoint HTTPS:</span><span class="sxs-lookup"><span data-stu-id="7805d-114">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="7805d-115">Il client .NET Core deve usare `https` nell'indirizzo del server per effettuare chiamate con una connessione protetta:</span><span class="sxs-lookup"><span data-stu-id="7805d-115">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="7805d-116">Tutte le implementazioni client di gRPC supportano TLS.</span><span class="sxs-lookup"><span data-stu-id="7805d-116">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="7805d-117">i client gRPC di altri linguaggi richiedono in genere il canale configurato con `SslCredentials` .</span><span class="sxs-lookup"><span data-stu-id="7805d-117">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="7805d-118">`SslCredentials`Specifica il certificato che verrà utilizzato dal client e deve essere utilizzato al posto di credenziali non sicure.</span><span class="sxs-lookup"><span data-stu-id="7805d-118">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="7805d-119">Per esempi di configurazione delle diverse implementazioni client di gRPC per l'uso di TLS, vedere [autenticazione gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="7805d-119">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="7805d-120">Chiamare un servizio gRPC con un certificato non attendibile/non valido</span><span class="sxs-lookup"><span data-stu-id="7805d-120">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="7805d-121">Il client gRPC .NET richiede che il servizio disponga di un certificato attendibile.</span><span class="sxs-lookup"><span data-stu-id="7805d-121">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="7805d-122">Quando si chiama un servizio gRPC senza un certificato attendibile, viene restituito il messaggio di errore seguente:</span><span class="sxs-lookup"><span data-stu-id="7805d-122">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="7805d-123">Eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="7805d-123">Unhandled exception.</span></span> <span data-ttu-id="7805d-124">System .NET. http. HttpRequestexception: non è stato possibile stabilire la connessione SSL. vedere l'eccezione interna.</span><span class="sxs-lookup"><span data-stu-id="7805d-124">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="7805d-125">---> System.Security.Authentication.AuthenticationException: Il certificato remoto non è stato ritenuto valido dalla procedura di convalida.</span><span class="sxs-lookup"><span data-stu-id="7805d-125">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="7805d-126">Questo errore può essere visualizzato se si sta testando l'app in locale e il certificato di sviluppo HTTPS ASP.NET Core non è attendibile.</span><span class="sxs-lookup"><span data-stu-id="7805d-126">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="7805d-127">Per istruzioni su come risolvere questo problema, vedere [Considerare attendibile il certificato di sviluppo di ASP.NET Core HTTPS in Windows e macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="7805d-127">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="7805d-128">Se si chiama un servizio gRPC in un altro computer e non è possibile considerare attendibile il certificato, il client gRPC può essere configurato in modo da ignorare il certificato non valido.</span><span class="sxs-lookup"><span data-stu-id="7805d-128">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="7805d-129">Il codice seguente usa [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) per consentire le chiamate senza un certificato attendibile:</span><span class="sxs-lookup"><span data-stu-id="7805d-129">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

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
> <span data-ttu-id="7805d-130">I certificati non attendibili devono essere usati solo durante lo sviluppo di app.</span><span class="sxs-lookup"><span data-stu-id="7805d-130">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="7805d-131">Le app di produzione devono sempre usare certificati validi.</span><span class="sxs-lookup"><span data-stu-id="7805d-131">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="7805d-132">Chiamare i servizi gRPC non sicuri con il client .NET Core</span><span class="sxs-lookup"><span data-stu-id="7805d-132">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="7805d-133">È necessaria una configurazione aggiuntiva per chiamare i servizi gRPC non protetti con il client .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7805d-133">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="7805d-134">Il client gRPC deve impostare l' `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` opzione su `true` e usare `http` nell'indirizzo del server:</span><span class="sxs-lookup"><span data-stu-id="7805d-134">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="7805d-135">Non è possibile avviare ASP.NET Core app gRPC in macOS</span><span class="sxs-lookup"><span data-stu-id="7805d-135">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="7805d-136">Gheppio non supporta HTTP/2 con TLS in macOS e versioni precedenti di Windows, ad esempio Windows 7.</span><span class="sxs-lookup"><span data-stu-id="7805d-136">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="7805d-137">Per impostazione predefinita, il ASP.NET Core modello e gli esempi di gRPC usano TLS.</span><span class="sxs-lookup"><span data-stu-id="7805d-137">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="7805d-138">Quando si tenta di avviare il server gRPC, viene visualizzato il messaggio di errore seguente:</span><span class="sxs-lookup"><span data-stu-id="7805d-138">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="7805d-139">Non è possibile eseguire l'associazione a nell' https://localhost:5001 interfaccia di loopback IPv4:' http/2 su TLS non è supportato in MacOS perché manca il supporto di ALPN '.</span><span class="sxs-lookup"><span data-stu-id="7805d-139">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="7805d-140">Per risolvere questo problema, configurare gheppio e il client gRPC per l'uso di HTTP/2 *senza* TLS.</span><span class="sxs-lookup"><span data-stu-id="7805d-140">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="7805d-141">Questa operazione deve essere eseguita solo durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="7805d-141">You should only do this during development.</span></span> <span data-ttu-id="7805d-142">Se non si usa TLS, i messaggi gRPC vengono inviati senza crittografia.</span><span class="sxs-lookup"><span data-stu-id="7805d-142">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="7805d-143">Gheppio deve configurare un endpoint HTTP/2 senza TLS in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7805d-143">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="7805d-144">Quando un endpoint HTTP/2 viene configurato senza TLS, l'endpoint [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) deve essere impostato su `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="7805d-144">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="7805d-145">`HttpProtocols.Http1AndHttp2`non può essere usato perché TLS è necessario per negoziare HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7805d-145">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="7805d-146">Senza TLS, tutte le connessioni all'endpoint vengono predefinite a HTTP/1.1 e le chiamate a gRPC hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="7805d-146">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="7805d-147">Il client di gRPC deve essere configurato anche per non usare TLS.</span><span class="sxs-lookup"><span data-stu-id="7805d-147">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="7805d-148">Per altre informazioni, vedere [chiamare i servizi gRPC non sicuri con il client .NET Core](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="7805d-148">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="7805d-149">HTTP/2 senza TLS deve essere usato solo durante lo sviluppo di app.</span><span class="sxs-lookup"><span data-stu-id="7805d-149">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="7805d-150">Le app di produzione devono sempre usare la sicurezza del trasporto.</span><span class="sxs-lookup"><span data-stu-id="7805d-150">Production apps should always use transport security.</span></span> <span data-ttu-id="7805d-151">Per ulteriori informazioni, vedere [considerazioni sulla sicurezza in gRPC per ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="7805d-151">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="7805d-152">gli asset C# di gRPC non sono generati da file con estensione proto</span><span class="sxs-lookup"><span data-stu-id="7805d-152">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="7805d-153">per la generazione di codice gRPC di client concreti e classi di base del servizio è necessario fare riferimento a file e strumenti protobuf da un progetto.</span><span class="sxs-lookup"><span data-stu-id="7805d-153">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="7805d-154">È necessario includere:</span><span class="sxs-lookup"><span data-stu-id="7805d-154">You must include:</span></span>

* <span data-ttu-id="7805d-155">file con *estensione proto* che si desidera utilizzare nel `<Protobuf>` gruppo di elementi.</span><span class="sxs-lookup"><span data-stu-id="7805d-155">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="7805d-156">Il progetto deve fare riferimento ai [file *. proto* importati](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) .</span><span class="sxs-lookup"><span data-stu-id="7805d-156">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="7805d-157">Riferimento al pacchetto per il pacchetto di [strumenti GRPC gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="7805d-157">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="7805d-158">Per ulteriori informazioni sulla generazione di asset gRPC C#, vedere <xref:grpc/basics> .</span><span class="sxs-lookup"><span data-stu-id="7805d-158">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="7805d-159">Per impostazione predefinita, un `<Protobuf>` riferimento genera un client concreto e una classe di base del servizio.</span><span class="sxs-lookup"><span data-stu-id="7805d-159">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="7805d-160">L'attributo dell'elemento di riferimento `GrpcServices` può essere usato per limitare la generazione di asset in C#.</span><span class="sxs-lookup"><span data-stu-id="7805d-160">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="7805d-161">Le `GrpcServices` Opzioni valide sono:</span><span class="sxs-lookup"><span data-stu-id="7805d-161">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="7805d-162">`Both`(impostazione predefinita quando non è presente)</span><span class="sxs-lookup"><span data-stu-id="7805d-162">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="7805d-163">Un ASP.NET Core app Web che ospita i servizi gRPC richiede solo la classe di base del servizio generata:</span><span class="sxs-lookup"><span data-stu-id="7805d-163">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="7805d-164">Un'app client gRPC che effettua chiamate gRPC richiede solo il client concreto generato:</span><span class="sxs-lookup"><span data-stu-id="7805d-164">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="7805d-165">I progetti WPF non sono in grado di generare asset C# gRPC da file. proto</span><span class="sxs-lookup"><span data-stu-id="7805d-165">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="7805d-166">I progetti WPF presentano un [problema noto](https://github.com/dotnet/wpf/issues/810) che impedisce il corretto funzionamento della generazione del codice gRPC.</span><span class="sxs-lookup"><span data-stu-id="7805d-166">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="7805d-167">Tutti i tipi di gRPC generati in un progetto WPF facendo riferimento ai `Grpc.Tools` file *. proto e. proto* creeranno errori di compilazione quando vengono utilizzati:</span><span class="sxs-lookup"><span data-stu-id="7805d-167">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="7805d-168">errore CS0246: Impossibile trovare il tipo o il nome dello spazio dei nomi "MyGrpcServices". manca una direttiva using o un riferimento a un assembly.</span><span class="sxs-lookup"><span data-stu-id="7805d-168">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="7805d-169">Per aggirare questo problema, è possibile:</span><span class="sxs-lookup"><span data-stu-id="7805d-169">You can workaround this issue by:</span></span>

1. <span data-ttu-id="7805d-170">Creare un nuovo progetto libreria di classi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7805d-170">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="7805d-171">Nel nuovo progetto aggiungere i riferimenti per abilitare la [generazione di codice C# da file \* \* . proto\* ](xref:grpc/basics#generated-c-assets):</span><span class="sxs-lookup"><span data-stu-id="7805d-171">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="7805d-172">Aggiungere un riferimento al pacchetto [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="7805d-172">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="7805d-173">Aggiungere i file \* \* . proto\* al `<Protobuf>` gruppo di elementi.</span><span class="sxs-lookup"><span data-stu-id="7805d-173">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="7805d-174">Nell'applicazione WPF aggiungere un riferimento al nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="7805d-174">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="7805d-175">L'applicazione WPF può utilizzare i tipi generati da gRPC dal nuovo progetto libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="7805d-175">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
