## <a name="grpc-not-supported-on-azure-app-service"></a>gRPC non supportato nel servizio app di Azure

> [!WARNING]
> [ASP.NET Core gRPC](xref:grpc/index) non è attualmente supportato nel servizio app di Azure o in IIS. L'implementazione HTTP/2 di Http.Sys non supporta le intestazioni di traccia della risposta HTTP su cui gRPC si basa. Per altre informazioni, vedere [questo problema in GitHub](https://github.com/dotnet/AspNetCore/issues/9020).
