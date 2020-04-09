---
title: Risolvere i problemi ed eseguire il debug di progetti ASP.NET CoreTroubleshoot and debug ASP.NET Core projects
author: Rick-Anderson
description: Riconoscere e risolvere i problemi di avvisi ed errori con i progetti ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 345967f08cf99ef5f18d0c9bcd59ab29c74454f1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511509"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Risolvere i problemi ed eseguire il debug di progetti ASP.NET CoreTroubleshoot and debug ASP.NET Core projects

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

I collegamenti seguenti forniscono indicazioni per la risoluzione dei problemi:The following links provide troubleshooting guidance:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Conferenza NDC (Londra, 2018): Diagnosi dei problemi nelle applicazioni ASP.NET Core](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET blog: Risoluzione dei problemi relativi alle prestazioni di base ASP.NET](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>Avvisi di .NET Core SDK

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>Sono installate entrambe le versioni a 32 bit e a 64 bit di .NET Core SDK

Nella finestra di dialogo **Nuovo progetto** per ASP.NET Core, è possibile che venga visualizzato il seguente avviso:

> Sono installate entrambe le versioni a 32 bit e a 64 bit di .NET Core SDK. Vengono visualizzati solo i modelli delle versioni\\a\\64 bit installati in 'C: Programmi dotnet\\sdk\\'.

Questo avviso viene visualizzato quando sono installate entrambe le versioni a 32 bit (x86) e a 64 bit (x64) di [.NET Core SDK.](https://dotnet.microsoft.com/download/dotnet-core) Motivi comuni per cui entrambe le versioni possono essere installate includono:

* In origine è stato scaricato il programma di installazione di .NET Core SDK utilizzando un computer a 32 bit, ma poi è stato copiato e installato in un computer a 64 bit.
* Il SDK .NET Core a 32 bit è stato installato da un'altra applicazione.
* È stata scaricata e installata la versione errata.

Disinstallare .NET Core SDK a 32 bit per evitare questo avviso. Disinstallare dal **Pannello di controllo** > **Programmi e funzionalità** > **Disinstallare o modificare un programma**. Se si comprende il motivo per cui si verifica l'avviso e le relative implicazioni, è possibile ignorare l'avviso.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>.NET Core SDK è installato in più posizioni

Nella finestra di dialogo **Nuovo progetto** per ASP.NET Core, è possibile che venga visualizzato il seguente avviso:

> .NET Core SDK viene installato in più posizioni. Vengono visualizzati solo i modelli degli\\SDK\\installati\\\\in 'C: Programmi dotnet sdk '.

Questo messaggio viene visualizzato quando si dispone di almeno un'installazione di .NET Core SDK in una directory esterna a *C:\\Programmi\\dotnet\\sdk\\*. In genere questo accade quando .NET Core SDK è stato distribuito in un computer utilizzando copia/incolla anziché il programma di installazione MSI.

Disinstallare tutti gli SDK e i runtime di .NET Core a 32 bit per evitare questo avviso. Disinstallare dal **Pannello di controllo** > **Programmi e funzionalità** > **Disinstallare o modificare un programma**. Se si comprende il motivo per cui si verifica l'avviso e le relative implicazioni, è possibile ignorare l'avviso.

### <a name="no-net-core-sdks-were-detected"></a>Non sono stati rilevati SDK di .NET Core

* Nella finestra di dialogo **Nuovo progetto** di Visual Studio per ASP.NET Core, è possibile che venga visualizzato il seguente avviso:

  > Non sono stati rilevati SDK di .NET Core, `PATH`assicurarsi che siano inclusi nella variabile di ambiente .

* Quando si `dotnet` esegue un comando, l'avviso viene visualizzato come:

  > Non è stato possibile trovare alcun SDK dotnet installato.

Questi avvisi vengono visualizzati quando la variabile `PATH` di ambiente non punta ad alcun SDK di .NET Core nel computer. Per risolvere questo problema:

* Installare .NET Core SDK. Ottenere il programma di installazione più recente da [.NET Downloads](https://dotnet.microsoft.com/download).
* Verificare `PATH` che la variabile di ambiente punti`C:\Program Files\dotnet\` al percorso in cui è `C:\Program Files (x86)\dotnet\` installato l'SDK (per 64 bit/x64 o per 32 bit/x86). Il programma di `PATH`installazione SDK in genere imposta il file . Installare sempre gli stessi SDK e runtime di bit sullo stesso computer.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>SDK mancante dopo l'installazione del pacchetto di hosting di .NET Core

L'installazione di [.NET Core Hosting](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) `PATH` Bundle modifica quando viene installato il runtime .NET Core in modo che punti alla`C:\Program Files (x86)\dotnet\`versione a 32 bit (x86) di .NET Core ( ). Ciò può causare SDK mancanti quando viene utilizzato il comando `dotnet` .NET Core a 32 bit (x86)[(non è stato rilevato alcun SDK di .NET Core](#no-net-core-sdks-were-detected)). Per risolvere questo `C:\Program Files\dotnet\` problema, passare `C:\Program Files (x86)\dotnet\` a `PATH`una posizione prima del file .

## <a name="obtain-data-from-an-app"></a>Ottenere dati da un'app

Se un'app è in grado di rispondere alle richieste, è possibile ottenere i seguenti dati dall'app utilizzando middleware:

* Metodo &ndash; di richiesta, schema, host, pathbase, percorso, stringa di query, intestazioni
* Connessione &ndash; Indirizzo IP remoto, porta remota, indirizzo IP locale, porta locale, certificato client
* Nome &ndash; identità, nome visualizzato
* Impostazioni di configurazione
* Variabili di ambiente

Inserire il codice [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) seguente `Startup.Configure` all'inizio della pipeline di elaborazione delle richieste del metodo. L'ambiente viene controllato prima dell'esecuzione del middleware per garantire che il codice venga eseguito solo nell'ambiente di sviluppo.

Per ottenere l'ambiente, utilizzare uno dei seguenti approcci:

* Inserirla `IHostingEnvironment` `Startup.Configure` nel metodo e controllare l'ambiente con la variabile locale. Il codice di esempio seguente viene illustrato questo approccio.

* Assegnare l'ambiente a `Startup` una proprietà nella classe. Controllare l'ambiente utilizzando la `if (Environment.IsDevelopment())`proprietà , ad esempio ).

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

## <a name="debug-aspnet-core-apps"></a>Eseguire il debug di app ASP.NET CoreDebug ASP.NET Core apps

I collegamenti seguenti forniscono informazioni sul debug di ASP.NET applicazioni Core.The following links provide information on debugging ASP.NET Core apps.

* [Debug di ASP Core su Linux](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [Debug di .NET Core su Unix su SSH](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Guida introduttiva: eseguire il debug di ASP.NET con il debugger di Visual Studio](/visualstudio/debugger/quickstart-debug-aspnet)
* Vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) per ulteriori informazioni di debug.
