---
title: Novità di ASP.NET Core 2.1
author: isaac2004
description: Informazioni sulle nuove funzionalità in ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- appsettings.json
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
uid: aspnetcore-2.1
ms.openlocfilehash: 199ca62f71df5c0ee5ca2f78caf53ad8d6be1179
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854561"
---
# <a name="whats-new-in-aspnet-core-21"></a>Novità di ASP.NET Core 2.1

Questo articolo evidenzia le modifiche più significative apportate ad ASP.NET Core 2.1, con collegamenti alla relativa documentazione.

## SignalR

SignalR è stato riscritto per ASP.NET Core 2,1. ASP.NET Core SignalR include una serie di miglioramenti:

* Un modello semplificato di scale-out.
* Un nuovo client JavaScript senza dipendenza jQuery.
* Un nuovo protocollo binario compresso basato su MessagePack.
* Supporto per protocolli personalizzati.
* Un nuovo modello di risposta streaming.
* Supporto per client basati su WebSocket.

Per ulteriori informazioni, vedere [ASP.NET Core SignalR ](xref:signalr/introduction).

## <a name="no-locrazor-class-libraries"></a>Razor librerie di classi

ASP.NET Core 2,1 semplifica la compilazione e l'inclusione Razor dell'interfaccia utente basata su in una libreria e la condivisione tra più progetti. Il nuovo Razor SDK consente di creare Razor file in un progetto di libreria di classi che può essere incluso in un pacchetto NuGet. Le visualizzazioni e le pagine delle librerie vengono individuate automaticamente e possono essere sostituite dall'app. Integrando Razor la compilazione nella compilazione:

* Si riduce notevolmente il tempo di avvio dell'app.
* Gli aggiornamenti rapidi alle Razor viste e alle pagine in fase di esecuzione sono ancora disponibili come parte di un flusso di lavoro di sviluppo iterativo.

Per altre informazioni, vedere [creare un'interfaccia utente riutilizzabile usando il Razor progetto libreria di classi](xref:razor-pages/ui-class).

## <a name="no-locidentity-ui-library--scaffolding"></a>Identity Impalcatura & della libreria dell'interfaccia utente

ASP.NET Core 2,1 fornisce [ASP.NET Core Identity](xref:security/authentication/identity) una [ Razor libreria di classi](xref:razor-pages/ui-class). Le app che includono Identity possono applicare il nuovo Identity ponteggi per aggiungere selettivamente il codice sorgente contenuto nella Identity Razor libreria di classi (RCL). Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento. Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione. Il codice generato ha la precedenza sullo stesso codice in Identity RCL.

Le app che **non** includono l'autenticazione possono applicare l' Identity impalcatura per aggiungere il Identity pacchetto RCL. È possibile scegliere di selezionare il Identity codice da generare.

Per ulteriori informazioni, vedere la pagina relativa [all'impalcatura Identity nei progetti ASP.NET Core](xref:security/authentication/scaffold-identity).

## <a name="https"></a>HTTPS

Vista la maggiore attenzione rivolta a sicurezza e privacy, è importante abilitare HTTPS per le app Web. L'imposizione HTTPS sta diventando sempre più rigida sul Web. I siti che non utilizzano HTTPS sono considerati non sicuri. Alcuni browser come Chromium e Mozilla hanno iniziato a imporre che le funzionalità Web siano usate da un contesto protetto. Il [Regolamento generale sulla protezione dei dati (GDPR) ](xref:security/gdpr) richiede l'uso di HTTPS per proteggere la privacy degli utenti. Usare HTTPS nell'ambiente di produzione è una questione critica, usarlo nell'ambiente di sviluppo può invece prevenire problemi di distribuzione, ad esempio collegamenti non sicuri. ASP.NET Core 2.1 include numerosi miglioramenti che semplificano l'uso di HTTPS nell'ambiente di sviluppo e la configurazione di HTTPS nell'ambiente di produzione. Per altre informazioni, vedere [Applicare HTTPS](xref:security/enforcing-ssl).

### <a name="on-by-default"></a>Attivato per impostazione predefinita

Per semplificare lo sviluppo di siti Web sicuri, HTTPS è ora abilitato per impostazione predefinita. A partire dalla versione 2.1, Kestrel è in ascolto su `https://localhost:5001` quando è disponibile un certificato di sviluppo locale. Viene creato un certificato di sviluppo nei casi seguenti:

* Come parte di completamento dell'installazione di .NET Core SDK, quando si usa SDK per la prima volta.
* Manualmente tramite il nuovo strumento `dev-certs`.

Per rendere attendibile il certificato, eseguire `dotnet dev-certs https --trust`.

### <a name="https-redirection-and-enforcement"></a>Imposizione e reindirizzamento HTTPS

Le app Web sono generalmente in ascolto sia su HTTP sia su HTTPS, ma reindirizzano poi tutto il traffico HTTP a HTTPS. Nella versione 2.1 è stato introdotto un middleware di reindirizzamento HTTPS specializzato che in modo intelligente reindirizza il traffico in base alla configurazione o all'associazione di porte server.

HTTPS può essere ulteriormente rafforzato tramite il [protocollo di sicurezza HSTS (sicurezza rigida per il trasporto di HTTP)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts). Il protocollo HSTS indica al browser di accedere sempre al sito tramite HTTPS. ASP.NET Core 2.1 aggiunge il middleware HSTS che supporta le opzioni per la validità massima, i sottodomini e l'elenco di precaricamento HSTS.

### <a name="configuration-for-production"></a>Configurazione per l'ambiente di produzione

Nell'ambiente di produzione, HTTPS deve essere configurato in modo esplicito. Nella versione 2.1 è stato aggiunto lo schema di configurazione predefinito per la configurazione di HTTPS per Kestrel. È possibile configurare le app in modo che possano usare gli elementi seguenti:

* Più endpoint, inclusi gli URL. Per altre informazioni, vedere [Implementazione del server Web Kestrel: Configurazione dell'endpoint](xref:fundamentals/servers/kestrel#endpoint-configuration).
* Il certificato da usare per HTTPS da un file su disco o da un archivio certificati.

## <a name="gdpr"></a>GDPR

ASP.NET Core offre API e modelli con cui è possibile soddisfare alcuni dei requisiti del [GDPR](https://www.eugdpr.org/). Per altre informazioni, vedere [Supporto per il Regolamento generale sulla protezione dei dati in ASP.NET Core](xref:security/gdpr). L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) illustra come usare e testare molte delle API e dei punti di estensione del GDPR aggiunti nei modelli ASP.NET Core 2.1.

## <a name="integration-tests"></a>Test di integrazione

È stato introdotto un nuovo pacchetto che semplifica la creazione e l'esecuzione di test. Con il pacchetto [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) è possibile gestire le attività seguenti:

* Copia il file di dipendenza (*\* . Deps*) dall'app testata nella cartella *bin* del progetto di test.
* Impostare la radice del contenuto sulla radice del progetto dell'app testata in modo che i file statici e le pagine/visualizzazioni siano rilevate durante l'esecuzione dei test.
* Specificare la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) per semplificare il bootstrap dell'app testata con [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).

Il test seguente usa [xUnit](https://xunit.github.io/) per verificare che la pagina di indice sia caricata con un codice di stato con esito positivo e con l'intestazione Content-Type corretta:

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

Per altre informazioni, vedere l'argomento [Test di integrazione](xref:test/integration-tests).

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T>

ASP.NET Core 2.1 aggiunge nuove convenzioni di programmazione che rendono più semplice la compilazione di API Web descrittive e ordinate. `ActionResult<T>` è una nuova convenzione aggiunta che consente a un'app di restituire un tipo di risposta o qualsiasi altro risultato dell'azione (come IActionResult) e al tempo stesso indicare il tipo di risposta. `[ApiController]` è un attributo aggiunto per acconsentire esplicitamente a convenzioni e comportamenti specifici per API Web.

Per altre informazioni, vedere [Creare API Web con ASP.NET Core](xref:web-api/index).

## <a name="ihttpclientfactory"></a>IHttpClientFactory

ASP.NET Core 2.1 include un nuovo servizio `IHttpClientFactory` che semplifica la configurazione e l'uso di istanze di `HttpClient` nelle app. `HttpClient` include già il concetto di delega di gestori concatenati per le richieste HTTP in uscita. Il factory:

* Esegue la registrazione di istanze di `HttpClient` per ogni client denominato in modo più intuitivo.
* Implementa un gestore Polly che consente di usare i criteri Polly per schemi Retry, CircuitBreakers e così via.

Per altre informazioni, vedere [Inizializzare richieste HTTP](xref:fundamentals/http-requests).

## <a name="kestrel-libuv-transport-configuration"></a>Configurazione del trasporto libuv di gheppio

Con la versione ASP.NET Core 2.1, il trasporto predefinito di Kestrel non si basa più su Libuv, ma su socket gestiti. Per ulteriori informazioni, vedere la pagina relativa all' [implementazione del server Web gheppio: configurazione del trasporto libuv](xref:fundamentals/servers/kestrel#libuv-transport-configuration).

## <a name="generic-host-builder"></a>Generatore di host generico

È stato introdotto il generatore di host generico (`HostBuilder`). È possibile usare il generatore per le app che non elaborano richieste HTTP (messaggistica, attività in background e così via).

Per altre informazioni, vedere [Host generico .NET](xref:fundamentals/host/generic-host).

## <a name="updated-spa-templates"></a>Modelli di applicazione a pagina singola aggiornati

Sono stati aggiornati i modelli di applicazione a pagina singola per Angular, React e React con Redux in modo da usare le strutture di progetto e i sistemi di compilazione standard per ogni framework.

Il modello Angular si basa sulla riga di comando di Angular mentre i modelli React su create-react-app.

Per altre informazioni, vedere:

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="no-locrazor-pages-search-for-no-locrazor-assets"></a>Razor Pagine ricerca di Razor Asset

In 2,1 le Razor pagine ricercano Razor risorse (ad esempio layout e parziali) nelle directory seguenti nell'ordine elencato:

1. Cartella Pages corrente.
1. */Pages/Shared/*
1. */Views/Shared/*

## <a name="no-locrazor-pages-in-an-area"></a>Razor Pagine in un'area

Razor Le pagine supportano ora le [aree](xref:mvc/controllers/areas). Per visualizzare un esempio di aree, creare una nuova Razor app Web di pagine con singoli account utente. Un' Razor app Web di pagine con account utente singoli include */areas/ Identity /pages*.

## <a name="mvc-compatibility-version"></a>Versione di compatibilità MVC

Il metodo <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> consente a un'app di acconsentire o rifiutare esplicitamente modifiche potenzialmente importanti del comportamento introdotte in ASP.NET Core MVC 2.1 o versioni successive.

Per altre informazioni, vedere <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>Eseguire la migrazione dalla versione 2.0 alla versione 2.1

Vedere [Eseguire la migrazione da ASP.NET Core 2.0 alla versione 2.1](xref:migration/20_21).

## <a name="additional-information"></a>Informazioni aggiuntive

Per l'elenco completo delle modifiche, vedere le [note sulla versione di ASP.NET Core 2.1](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).
