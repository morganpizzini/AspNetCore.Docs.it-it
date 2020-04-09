---
page_type: sample
description: Questa esercitazione illustra come creare un servizio gRPC e un client gRPC in ASP.NET Core.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: create-grpc-client
ms.openlocfilehash: b9feb9eed62177358fffc0d7da582f625a431e32
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660930"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a>Creare un client e un server gRPC in ASP.NET Core 3.0 con Visual Studio

Questa esercitazione illustra come creare un client [gRPC](https://grpc.io/docs/guides/) .NET Core e un server gRPC ASP.NET Core.

Al termine, si otterrà un client gRPC che comunica con il servizio Greeter gRPC.

Le attività di questa esercitazione sono le seguenti:

* Creare un server gRPC.
* Creare un client gRPC.
* Testare il servizio client gRPC con il servizio Greeter gRPC.

## <a name="create-a-grpc-service"></a>Creare un servizio gRPC

* Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.
* Nella finestra di dialogo **Crea un nuovo progetto** selezionare **Applicazione Web ASP.NET Core**.
* Fare clic su **Avanti**.
* Denominare il progetto **GrpcGreeter**. È importante denominare il progetto *GrpcGreeter* in modo che gli spazi dei nomi corrispondano nell'operazione copia/incolla del codice.
* Selezionare **Crea**
* Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core**:
  * Selezionare **.NET Core** e **ASP.NET Core 3.0** negli elenchi a discesa. 
  * Selezionare il modello del **servizio gRPC**.
  * Selezionare **Crea**

### <a name="run-the-service"></a>Eseguire il servizio

* Premere `Ctrl+F5` per eseguire il servizio gRPC senza il debugger.

  Visual Studio esegue il servizio in un prompt dei comandi.

I log indicano che il servizio è in ascolto su `https://localhost:5001`.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

> [!NOTE]
> Il modello gRPC è configurato per l'uso di [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246). i client gRPC devono usare HTTPS per chiamare il server.
>
> macOS non supporta ASP.NET Core gRPC con TLS. Per eseguire correttamente i servizi gRPC in macOS, è necessaria una configurazione aggiuntiva. Per altre informazioni, vedere [gRPC e ASP.NET Core in macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).

### <a name="examine-the-project-files"></a>Esaminare i file di progetto

File di progetto *GrpcGreeter*:

* *greet.proto*: Il file *Protos/greet.proto* definisce il `Greeter` file gRPC e viene utilizzato per generare le risorse del server gRPC. Per altre informazioni, vedere [Introduzione a gRPC](xref:grpc/index).
* *Cartella Servizi:* contiene l'implementazione del `Greeter` servizio.
* *appSettings.json*: Contiene i dati di configurazione, ad esempio il protocollo utilizzato da Kestrel. Per altre informazioni, vedere <xref:fundamentals/configuration/index>.
* *Program.cs*: contiene il punto di ingresso per il servizio gRPC. Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.
* *Startup.cs:* contiene il codice che configura il comportamento dell'app. Per altre informazioni, vedere [Avvio dell'app](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Creare il client gRPC in un'app console .NET

* Aprire una seconda istanza di Visual Studio.
* Selezionare **File** > **nuovo** > **progetto** dalla barra dei menu.
* Nella finestra di dialogo **Crea un nuovo progetto** selezionare **App console (.NET Core)**.
* Fare clic su **Avanti**.
* Nella casella di testo **Nome** immettere "GrpcGreeterClient".
* Selezionare **Create** (Crea).

### <a name="add-required-packages"></a>Aggiungere i pacchetti necessari

Per il progetto client gRPC sono richiesti i pacchetti seguenti:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), che contiene il client .NET Core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), che contiene le API per i messaggi protobuf per C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), che contiene il supporto di strumenti C# per i file protobuf. Il pacchetto di strumenti non è necessario in fase di esecuzione, quindi la dipendenza è contrassegnata come `PrivateAssets="All"`.

Installare i pacchetti tramite la Console di gestione pacchetti (PMC) o Gestisci pacchetti NuGet.

#### <a name="pmc-option-to-install-packages"></a>Opzione con la console di Gestione pacchetti per installare i pacchetti

* In Visual Studio selezionare **Console** > di gestione pacchetti di Strumenti**NuGet Gestione** > **pacchetti**
* Dalla finestra **Console di Gestione pacchetti** passare alla directory in cui si trova il file *GrpcGreeterClient.csproj*.
* Eseguire i comandi seguenti:

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Opzione Gestisci pacchetti NuGet per installare i pacchetti

* Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** > Gestire i pacchetti NuGetRight-click the project in Solution Explorer**Manage NuGet Packages**
* Selezionare la scheda **Sfoglia**.
* Immettere **Grpc.Net.Client** nella casella di ricerca.
* Selezionare il pacchetto **Grpc.Net.Client** dalla scheda **Sfoglia** e fare clic su **Installa**.
* Ripetere la procedura per `Google.Protobuf` e `Grpc.Tools`.

### <a name="add-greetproto"></a>Aggiungere greet.proto

* Creare una cartella **Protos** nel progetto client gRPC.
* Copiare il file **Protos\greet.proto** dal servizio Greeter gRPC al progetto client gRPC.
* Modificare il file di progetto *GrpcGreeterClient.csproj*:

  Fare clic con il pulsante destro del mouse sul progetto e scegliere **Modifica file di progetto**.

* Aggiungere un gruppo di elementi con un elemento `<Protobuf>` che fa riferimento al file **greet.proto**:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Creare il client Greeter

Compilare il progetto per creare i tipi nello spazio dei nomi `GrpcGreeter`. I tipi `GrpcGreeter` vengono generati automaticamente dal processo di compilazione.

Aggiornare il file *Program.cs* del client gRPC con il codice seguente:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using GrpcGreeter;
using Grpc.Net.Client;

namespace GrpcGreeterClient
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // The port number(5001) must match the port of the gRPC server.
            var channel = GrpcChannel.ForAddress("https://localhost:5001");
            var client = new Greeter.GreeterClient(channel);
            var reply = await client.SayHelloAsync(
                              new HelloRequest { Name = "GreeterClient" });
            Console.WriteLine("Greeting: " + reply.Message);
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

*Program.cs* contiene la logica e il punto di ingresso per il client gRPC.

Il client Greeter viene creato come descritto di seguito:

* Creare un'istanza di `GrpcChannel` contenente le informazioni per creare la connessione al servizio gRPC.
* Utilizzo `GrpcChannel` di per costruire il client Greeter.

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Testare il client gRPC con il servizio Greeter gRPC

* Nel servizio Greeter premere `Ctrl+F5` per avviare il server senza il debugger.
* Nel progetto `GrpcGreeterClient` premere `Ctrl+F5` per avviare il client senza il debugger.

Il client invia un saluto al servizio con un messaggio contenente il nome "GreeterClient". Il servizio invia il messaggio "Hello GreeterClient" come risposta. La risposta "Hello GreeterClient" viene visualizzata al prompt dei comandi:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Il servizio gRPC registra i dettagli della chiamata con esito positivo nei log scritti al prompt dei comandi.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="docs-help--next-steps-for-grpc"></a>Guida di docs e passaggi successivi per gRPC

* [Introduzione a gRPC su ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [Servizi gRPC con C#](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [Migrazione di servizi gRPC da C-core ad ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
