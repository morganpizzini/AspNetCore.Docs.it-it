## <a name="grpc-not-supported-on-azure-app-service"></a>gRPC non supportato nel servizio app Azure

> [!WARNING]
> [ASP.NET Core gRPC](xref:grpc/index) non è attualmente supportato nel servizio app Azure o in IIS. L'implementazione HTTP/2 di http. sys non supporta le intestazioni finali della risposta HTTP su cui si basa gRPC. Per altre informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore/issues/9020).
