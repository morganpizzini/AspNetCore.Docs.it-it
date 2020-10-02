Reset consente al server di reimpostare una richiesta HTTP/2 con un codice di errore specificato. Una richiesta di reimpostazione viene considerata interrotta.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` Nell'esempio di codice precedente viene specificato il `INTERNAL_ERROR` codice di errore. Per ulteriori informazioni sui codici di errore HTTP/2, vedere la [sezione codice di errore della specifica http/2](https://tools.ietf.org/html/rfc7540#page-50).