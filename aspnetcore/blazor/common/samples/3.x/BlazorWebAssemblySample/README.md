# <a name="blazor-webassembly-sample-app"></a>App di esempio Blazor WebAssembly

In questo esempio viene illustrato l'utilizzo di scenari Blazor descritti nella documentazione di Blazor.

## <a name="call-web-api-example"></a>Immagine dell'API Web delle chiamate

L'esempio di API Web richiede un'API Web in esecuzione basata sull'app di esempio per l'argomento <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">Creare un'API Web con ASP.NET Core,</a> che per impostazione predefinita usa la stessa porta HTTPS (5001) dell'app di esempio Blazor. Per usare entrambe le app contemporaneamente sullo stesso computer, modificare la porta dell'API Web (ad esempio, usare la porta 10000). L'app di esempio effettua `https://localhost:10000/api/TodoItems`richieste all'API Web in . Se viene utilizzato un indirizzo API `ServiceEndpoint` Web diverso, aggiornare `@code` il valore costante nel blocco del componente Razor.</p>

L'app di esempio effettua una richiesta di `http://localhost:5000` condivisione delle risorse tra origini (CORS) da o verso l'API Web.The sample app makes a <a href="https://docs.microsoft.com/aspnet/core/security/cors">cross-origin resource sharing (CORS)</a> request from or `https://localhost:5001` to the web API. Le credenziali (cookie/intestazioni di autorizzazione) sono consentite. Aggiungere la seguente configurazione middleware CORS `Startup.Configure` al metodo dell'API Web:</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Regolare i domini `WithOrigins` e le porte di come necessario per l'app Blazor.

L'API Web è configurata per CORS per consentire l'autorizzazione di cookie/intestazioni e richieste dal codice client, ma l'API Web creata dall'esercitazione non autorizza effettivamente le richieste. Vedere gli <a href="https://docs.microsoft.com/aspnet/core/security/">articoli ASP.NET Core Security and Identity</a> per istruzioni sull'implementazione.
