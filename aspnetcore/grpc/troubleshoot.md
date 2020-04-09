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
# <a name="troubleshoot-grpc-on-net-core"></a>Risolvere i problemi relativi a gRPC in .NET Core

Di [James Newton-King](https://twitter.com/jamesnk)

In questo documento vengono illustrati i problemi comuni durante lo sviluppo di app gRPC in .NET.

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>Mancata corrispondenza tra configurazione SSL/TLS del client e del servizio

Il modello gRPC e gli esempi [utilizzano Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) per proteggere i servizi gRPC per impostazione predefinita. I client gRPC devono utilizzare una connessione protetta per chiamare correttamente i servizi gRPC protetti.

È possibile verificare che il servizio gRPC di ASP.NET tramite utilizzi TLS nei registri scritti all'avvio dell'app. Il servizio sarà in ascolto su un endpoint HTTPS:The service will be listening on an HTTPS endpoint:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

Il client .NET `https` Core deve utilizzare nell'indirizzo del server per effettuare chiamate con una connessione protetta:The .NET Core client must use in the server address to make calls with a secured connection:

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Tutte le implementazioni client gRPC supportano TLS. I client gRPC di altre lingue `SslCredentials`richiedono in genere il canale configurato con . `SslCredentials`specifica il certificato che verrà utilizzato dal client e deve essere utilizzato al posto delle credenziali non sicure. Per esempi di configurazione delle diverse implementazioni client gRPC per l'utilizzo di TLS, vedere [Autenticazione gRPC](https://www.grpc.io/docs/guides/auth/).

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Chiamare un servizio gRPC con un certificato non attendibile/non validoCall a gRPC service with an untrusted/invalid certificate

Il client gRPC .NET richiede che il servizio disponga di un certificato attendibile. Quando si chiama un servizio gRPC senza un certificato attendibile, viene restituito il seguente messaggio di errore:

> Eccezione non gestita. System.Net.Http.HttpRequestException: Impossibile stabilire la connessione SSL, vedere eccezione interna.
> ---> System.Security.Authentication.AuthenticationException: Il certificato remoto non è stato ritenuto valido dalla procedura di convalida.

Questo errore può essere visualizzato se si sta testando l'app in locale e il certificato di sviluppo HTTPS di ASP.NET core non è attendibile. Per istruzioni su come risolvere questo problema, vedere [Considerare attendibile il certificato di sviluppo di ASP.NET Core HTTPS in Windows e macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

Se si chiama un servizio gRPC su un altro computer e non si riesce a considerare attendibile il certificato, il client gRPC può essere configurato per ignorare il certificato non valido. Il codice seguente usa HttpClientHandler.ServerCertificateCustomValidationCallback per consentire chiamate senza un certificato attendibile:The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:

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
> I certificati non attendibili devono essere usati solo durante lo sviluppo dell'app. Le app di produzione devono sempre usare certificati validi.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Chiamare servizi gRPC non sicuri con il client .NET CoreCall insecure gRPC services with .NET Core client

È necessaria una configurazione aggiuntiva per chiamare servizi gRPC non sicuri con il client .NET Core. Il client gRPC `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` deve `true` impostare `http` l'opzione su e utilizzarla nell'indirizzo del server:

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>Impossibile avviare ASP.NET'app gRPC core su macOS

Kestrel non supporta HTTP/2 con TLS su macOS e versioni precedenti di Windows come Windows 7. Il modello gRPC ASP.NET Core e gli esempi utilizzano TLS per impostazione predefinita. Quando si tenta di avviare il server gRPC, verrà visualizzato il seguente messaggio di errore:

> Impossibile eseguire https://localhost:5001 l'associazione nell'interfaccia di loopback IPv4: 'HTTP/2 su TLS non è supportato in macOS a causa del supporto ALPN mancante'.

Per risolvere questo problema, configurare Kestrel e il client gRPC per utilizzare HTTP/2 *senza* TLS. È consigliabile eseguire questa operazione solo durante lo sviluppo. Se non si utilizza TLS, i messaggi gRPC verranno inviati senza crittografia.

Kestrel deve configurare un endpoint HTTP/2 senza TLS in *Program.cs:*

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

Quando un endpoint HTTP/2 è configurato senza TLS, [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) dell'endpoint deve essere impostato su `HttpProtocols.Http2`. `HttpProtocols.Http1AndHttp2`non può essere utilizzato perché TLS è necessario per negoziare HTTP/2. Senza TLS, tutte le connessioni all'endpoint vengono tutte predefinite a HTTP/1.1 e le chiamate gRPC hanno esito negativo.

Il client gRPC deve inoltre essere configurato per non utilizzare TLS. Per ulteriori informazioni, consultate [Chiamare servizi gRPC non sicuri con il client .NET Core.](#call-insecure-grpc-services-with-net-core-client)

> [!WARNING]
> HTTP/2 senza TLS deve essere usato solo durante lo sviluppo dell'app. Le app di produzione devono sempre usare la sicurezza del trasporto. Per ulteriori informazioni, vedere [Considerazioni sulla sicurezza in gRPC per ASP.NET Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>Le risorse di gRPC di C

La generazione di codice gRPC di client concreti e classi di base del servizio richiede l'invio di file e strumenti protobuf da un progetto. È necessario includere:

* *.proto* che si desidera `<Protobuf>` utilizzare nel gruppo di elementi. [I file *.proto* importati](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) devono fare riferimento al progetto.
* Riferimento del pacchetto al pacchetto di strumenti gRPC [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).

Per ulteriori informazioni sulla generazione di asset <xref:grpc/basics>gRPC in C, vedere .

Per impostazione `<Protobuf>` predefinita, un riferimento genera un client concreto e una classe base del servizio. L'attributo `GrpcServices` dell'elemento di riferimento può essere utilizzato per limitare la generazione di asset di C. Le `GrpcServices` opzioni valide sono:

* `Both`(impostazione predefinita quando non presente)
* `Server`
* `Client`
* `None`

Un'app Web ASP.NET Core che ospita i servizi gRPC richiede solo la classe di base del servizio generata:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Un'applicazione client gRPC che effettua chiamate gRPC richiede solo il client concreto generato:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a>Progetti WPF in grado di generare risorse gRPC di C

I progetti WPFWPF presentano un [problema noto](https://github.com/dotnet/wpf/issues/810) che impedisce il corretto funzionamento della generazione di codice gRPC. Qualsiasi tipo gRPC generato in un `Grpc.Tools` progetto WPF facendo riferimento ai file *.proto* creerà errori di compilazione quando utilizzati:

> Errore CS0246: Impossibile trovare il nome del tipo o dello spazio dei nomi 'MyGrpcServices' (manca una direttiva using o un riferimento all'assembly?)

È possibile risolvere questo problema:

1. Creare un nuovo progetto di libreria di classi .NET Core.Create a new .NET Core class library project.
2. Nel nuovo progetto, aggiungere i riferimenti per consentire la generazione di [codice C , dai * \** file con estensione proto](xref:grpc/basics#generated-c-assets):
    * Aggiungere un riferimento al pacchetto [Grpc.Tools.Add](https://www.nuget.org/packages/Grpc.Tools/) a package reference to Grpc.Tools package.
    * Aggiungere * \** file con estensione `<Protobuf>` proto al gruppo di elementi.
3. Nell'applicazione WPFWPF aggiungere un riferimento al nuovo progetto.

L'applicazione WPFWPF può usare i tipi generati gRPC dal nuovo progetto di libreria di classi.

[!INCLUDE[](~/includes/gRPCazure.md)]
