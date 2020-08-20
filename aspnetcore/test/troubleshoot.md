---
title: Risolvere i problemi ed eseguire il debug di progetti ASP.NET Core
author: Rick-Anderson
description: Riconoscere e risolvere i problemi di avvisi ed errori con i progetti ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
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
uid: test/troubleshoot
ms.openlocfilehash: 24781e7689a1a238e9d2fa9666fa61a8642135c3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632135"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Risolvere i problemi ed eseguire il debug di progetti ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

I collegamenti seguenti forniscono indicazioni sulla risoluzione dei problemi:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC Conference (Londra, 2018): diagnosi dei problemi nelle applicazioni ASP.NET Core](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [Blog di ASP.NET: risoluzione dei problemi di prestazioni ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>Avvisi di .NET Core SDK

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>Sono installate entrambe le versioni a 32 bit e 64 bit del .NET Core SDK

Nella finestra di dialogo **nuovo progetto** per ASP.NET Core, è possibile che venga visualizzato il seguente avviso:

> Sono installate entrambe le versioni a 32 bit e a 64 bit del .NET Core SDK. Vengono visualizzati solo i modelli delle versioni a 64 bit installate in ' C: \\ Program Files \\ DotNet \\ SDK \\ '.

Questo avviso viene visualizzato quando vengono installate entrambe le versioni a 32 bit (x86) e 64-bit (x64) del [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) . Di seguito sono elencati i motivi comuni per cui è possibile installare entrambe le versioni:

* Il programma di installazione .NET Core SDK è stato scaricato in origine con un computer a 32 bit, ma è stato copiato e installato in un computer a 64 bit.
* Il .NET Core SDK a 32 bit è stato installato da un'altra applicazione.
* È stata scaricata e installata la versione errata.

Disinstallare la .NET Core SDK a 32 bit per evitare questo avviso. Disinstallare da **Pannello di controllo**  >  **programmi e funzionalità**  >  **disinstallare o modificare un programma**. Se si comprende il motivo per cui si verifica l'avviso e le relative implicazioni, è possibile ignorare l'avviso.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>Il .NET Core SDK viene installato in più posizioni

Nella finestra di dialogo **nuovo progetto** per ASP.NET Core, è possibile che venga visualizzato il seguente avviso:

> Il .NET Core SDK viene installato in più posizioni. Vengono visualizzati solo i modelli degli SDK installati in ' C: \\ programmi \\ DotNet \\ SDK \\ '.

Questo messaggio viene visualizzato quando si dispone di almeno un'installazione del .NET Core SDK in una directory al di fuori di *C: \\ Program Files \\ DotNet \\ SDK \\ *. Questa situazione si verifica in genere quando il .NET Core SDK è stato distribuito in un computer tramite copia/incolla anziché il programma di installazione MSI.

Disinstallare tutti i runtime e gli SDK di .NET Core a 32 bit per evitare questo avviso. Disinstallare da **Pannello di controllo**  >  **programmi e funzionalità**  >  **disinstallare o modificare un programma**. Se si comprende il motivo per cui si verifica l'avviso e le relative implicazioni, è possibile ignorare l'avviso.

### <a name="no-net-core-sdks-were-detected"></a>Non sono stati rilevati SDK .NET Core

* Nella finestra di dialogo **nuovo progetto** di Visual Studio per ASP.NET Core, è possibile che venga visualizzato il seguente avviso:

  > Non sono stati rilevati SDK .NET Core, assicurarsi che siano inclusi nella variabile di ambiente `PATH` .

* Quando si esegue un `dotnet` comando, l'avviso viene visualizzato come segue:

  > Non è stato possibile trovare gli SDK DotNet installati.

Questi avvisi vengono visualizzati quando la variabile `PATH` di ambiente non punta ad alcun SDK di .NET Core nel computer. Per risolvere questo problema:

* Installare .NET Core SDK. Ottenere il programma di installazione più recente da [download di .NET](https://dotnet.microsoft.com/download).
* Verificare che la `PATH` variabile di ambiente punti al percorso in cui è installato l'SDK ( `C:\Program Files\dotnet\` per a 64 bit/x64 o `C:\Program Files (x86)\dotnet\` per 32 bit/x86). Il programma di installazione dell'SDK normalmente imposta `PATH` . Installare sempre gli stessi SDK e Runtime bit nello stesso computer.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>SDK mancante dopo l'installazione del bundle di hosting .NET Core

L'installazione del [bundle di hosting .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifica `PATH` quando installa il runtime di .NET Core in modo che punti alla versione 32 bit (x86) di .NET Core ( `C:\Program Files (x86)\dotnet\` ). Questo può causare la mancata presenza di SDK quando viene usato il comando .NET Core a 32 bit (x86) `dotnet` ([non sono stati rilevati SDK .NET Core](#no-net-core-sdks-were-detected)). Per risolvere il problema, passare `C:\Program Files\dotnet\` a una posizione precedente `C:\Program Files (x86)\dotnet\` in `PATH` .

## <a name="obtain-data-from-an-app"></a>Ottenere dati da un'app

Se un'app è in grado di rispondere alle richieste, è possibile ottenere i dati seguenti dall'app usando il middleware:

* Richiesta: metodo, schema, host, pathbase, percorso, stringa di query, intestazioni
* Connessione: indirizzo IP remoto, porta remota, indirizzo IP locale, porta locale, certificato client
* Identity: Nome, nome visualizzato
* Impostazioni di configurazione
* Variabili di ambiente

Inserire il codice [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) seguente all'inizio della pipeline di `Startup.Configure` elaborazione delle richieste del metodo. L'ambiente viene verificato prima dell'esecuzione del middleware per garantire che il codice venga eseguito solo nell'ambiente di sviluppo.

Per ottenere l'ambiente, usare uno degli approcci seguenti:

* Inserire l'oggetto `IHostingEnvironment` nel `Startup.Configure` metodo e controllare l'ambiente con la variabile locale. Il codice di esempio seguente illustra questo approccio.

* Assegnare l'ambiente a una proprietà nella `Startup` classe. Controllare l'ambiente utilizzando la proprietà (ad esempio, `if (Environment.IsDevelopment())` ).

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a>Eseguire il debug di app ASP.NET Core

I collegamenti seguenti forniscono informazioni sul debug di ASP.NET Core app.

* [Debug di ASP core in Linux](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [Debug di .NET Core su UNIX tramite SSH](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Guida introduttiva: eseguire il debug di ASP.NET con il debugger di Visual Studio](/visualstudio/debugger/quickstart-debug-aspnet)
* Per ulteriori informazioni sul debug, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) .
