> [!WARNING]
> [ASP.NET Core gRPC](xref:grpc/index) non è attualmente supportato nel servizio app Azure o in IIS. L'implementazione HTTP/2 di Http.Sys non supporta le intestazioni finali della risposta HTTP su cui si basa gRPC. Per altre informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore/issues/9020).
