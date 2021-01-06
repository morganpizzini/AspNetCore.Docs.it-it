---
page_type: sample
description: Informazioni su come aggiungere Swashbuckle al progetto dell'API Web ASP.NET Core per integrare l'interfaccia utente di Swagger.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: e02247325f430b0ce23dbb3f5bc344a60a1a164a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "78659936"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Introduzione a Swashbuckle e ad ASP.NET Core

Quando si usa un'API Web, la comprensione dei vari metodi che la compongono può risultare complessa per gli sviluppatori. [Swagger](https://swagger.io/), detto anche [OpenAPI](https://www.openapis.org/), risolve il problema della generazione di pagine della Guida e della documentazione utili per le API Web. Offre vantaggi quali la documentazione interattiva, la generazione di SDK client e l'individuabilità delle API.

In questo esempio viene mostrata l'implementazione .NET di [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).

## <a name="add-and-configure-swagger-middleware"></a>Aggiungere e configurare il middleware di Swagger

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });
}
```

Nel metodo `Startup.Configure` abilitare il middleware per la gestione del documento JSON generato e l'interfaccia utente di Swagger:

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1"); 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

La chiamata del metodo `UseSwaggerUI` precedente abilita il [middleware dei file statici](https://docs.microsoft.com/aspnet/core/fundamentals/static-files). Se la destinazione è .NET Framework o .NET Core 1. x, aggiungere il pacchetto NuGet [Microsoft. AspNetCore. staticfiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al progetto.

Avviare l'app e passare a `http://localhost:<port>/swagger/v1/swagger.json`. Il documento generato che descrive gli endpoint viene illustrato in [Specifica Swagger (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).

L'interfaccia utente di Swagger è disponibile in `http://localhost:<port>/swagger`. Esplorare l'API tramite l'interfaccia utente di Swagger e incorporarla in altri programmi.

> [!TIP]
> Per gestire l'interfaccia utente di Swagger nella radice dell'app (`http://localhost:<port>/`), impostare la proprietà `RoutePrefix` su una stringa vuota:
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

Se si usano le directory con ISS o il proxy inverso, impostare l'endpoint Swagger su un percorso relativo tramite il prefisso `./`. Ad esempio: `./swagger/v1/swagger.json`. L'uso di `/swagger/v1/swagger.json` indica all'app di cercare il file JSON nella vera radice dell'URL (con il prefisso della route, se usato). Usare, ad esempio, `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` invece di `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.

## <a name="customize-and-extend"></a>Personalizzare ed estendere

Swagger offre opzioni per documentare il modello a oggetti e personalizzare l'interfaccia utente in modo che corrisponda al tema.

Nella classe `Startup` aggiungere gli spazi dei nomi seguenti:
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>Informazioni e descrizione API

L'azione di configurazione passata al metodo `AddSwaggerGen` aggiunge informazioni come ad esempio autore, licenza e descrizione:

```csharp
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

L'interfaccia utente di Swagger visualizza le informazioni sulla versione:

![Interfaccia utente di Swagger con informazioni sulla versione: descrizione, autore e altri collegamenti](sample_images/custom-info.png)

### <a name="xml-comments"></a>Commenti in XML

I commenti XML possono essere abilitati con gli approcci seguenti:

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Modifica <nome_progetto>.csproj**.
* Aggiungere manualmente le righe evidenziate al file con estensione *csproj*:

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Dal *riquadro della soluzione* premere **controllo** e fare clic sul nome del progetto. Passare a **strumenti**  >  **modifica file**.
* Aggiungere manualmente le righe evidenziate al file con estensione *csproj*:

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aggiungere manualmente le righe evidenziate al file con estensione *csproj*:

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

Abilitando i commenti XML, viene eseguito il debug delle informazioni per membri e tipi pubblici non documentati. I membri e tipi non documentati sono indicati nel messaggio di avviso. Ad esempio, il messaggio seguente indica una violazione del codice di avviso 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Per eliminare gli avvisi per l'intero progetto, definire un elenco delimitato da punto e virgola di codici di avviso da ignorare nel file di progetto. L'aggiunta di codici di avviso a `$(NoWarn);` applica anche i [valori predefiniti di C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

Per eliminare gli avvisi solo per membri specifici, racchiudere il codice in direttive del preprocessore [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning). Questo approccio è utile per il codice che non deve essere esposto tramite la documentazione API. Nell'esempio seguente il codice di avviso CS1591 viene ignorato per l'intera `Program` classe. L'imposizione del codice di avviso viene ripristinata alla chiusura della definizione della classe. Specificare più codici di avviso con un elenco delimitato da virgole.

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

Configurare Swagger per usare il file XML che viene generato con le istruzioni precedenti. Per Linux o sistemi operativi diversi da Windows, i percorsi e i nomi di file possono fare distinzione tra maiuscole e minuscole. Ad esempio, un file *ToDoApi.XML* è valido in Windows, ma non in CentOS.

```csharp
/// NOTE LAST 3 LINES IN THIS SNIPPET
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "ToDo API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = new Uri("https://example.com/terms"),
            Contact = new OpenApiContact
            {
                Name = "Shayne Boyer",
                Email = string.Empty,
                Url = new Uri("https://twitter.com/spboyer"),
            },
            License = new OpenApiLicense
            {
                Name = "Use under LICX",
                Url = new Uri("https://example.com/license"),
            }
        });

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
}
```

Nel codice precedente, la [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) viene usata per compilare un nome file XML corrispondente a quello del progetto API Web. La proprietà [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) viene usata per costruire un percorso del file XML. Alcune funzionalità di Swagger (ad esempio, schemi di parametri di input o i metodi HTTP e i codici di risposta dai rispettivi attributi) funzionano senza l'uso di un file di documentazione XML. Per la maggior parte delle funzionalità, vale a dire i riepiloghi dei metodi e le descrizioni dei parametri e dei codici di risposta, l'uso di un file XML è obbligatorio.

Se si aggiungono commenti con barra tripla a un'azione si migliora Swagger UI aggiungendo la descrizione all'intestazione della sezione. Aggiungere un [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) elemento sopra l' `Delete` azione:

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
/// <param name="id"></param>        
[HttpDelete("{id}")]
public IActionResult Delete(long id)
{
    var todo = _context.TodoItems.Find(id);

    if (todo == null)
    {
        return NotFound();
    }

    _context.TodoItems.Remove(todo);
    _context.SaveChanges();

    return NoContent();
}
```
L'interfaccia utente di Swagger visualizza il testo interno dell'elemento `<summary>` del codice precedente:

![Interfaccia utente di Swagger che visualizza il commento XML "Deletes a specific TodoItem." per il metodo DELETE](sample_images/triple-slash-comments.png)

L'interfaccia utente è determinata dallo schema JSON generato:

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```
Aggiungere un [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) elemento alla `Create` documentazione del metodo di azione. In questo modo vengono integrate le informazioni specificate nell'elemento `<summary>` e l'interfaccia utente di Swagger risulta più affidabile. Il contenuto dell'elemento `<remarks>` può essere costituito da testo, JSON o XML.

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
Si notino i miglioramenti dell'interfaccia utente con questi commenti aggiuntivi:

![Interfaccia utente di Swagger con commenti aggiuntivi visualizzati](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a>Annotazioni dei dati

Contrassegnare il modello con attributi, disponibile nello spazio dei nomi [System. ComponentModel. DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) , per guidare i componenti dell'interfaccia utente di spavalderia.

Aggiungere l'attributo `[Required]` alla proprietà `Name` della classe `TodoItem`:

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace TodoApi.Models
{
    public class TodoItem
    {
        public long Id { get; set; }

        [Required]
        public string Name { get; set; }

        [DefaultValue(false)]
        public bool IsComplete { get; set; }
    }
}
```

La presenza di questo attributo modifica il comportamento dell'interfaccia utente e lo schema JSON sottostante:

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

Aggiungere l'attributo `[Produces("application/json")]` al controller API. Il suo scopo consiste nel dichiarare che le azioni del controller supportano un tipo di contenuto della risposta di *application/json*:

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
L'elenco a discesa **tipo di contenuto della risposta** seleziona questo tipo di contenuto come impostazione predefinita per le azioni Get del controller:

![Interfaccia utente di Swagger con tipo di contenuto di risposta predefinito](sample_images/json-response-content-type.png)

Con l'aumento dell'uso delle annotazioni dei dati nell'API Web, le pagine della Guida dell'API e dell'interfaccia utente diventano più descrittive e utili.

### <a name="describe-response-types"></a>Descrivere i tipi di risposta

La principale preoccupazione degli sviluppatori che utilizzano un'API Web è ciò che viene restituito, in particolare i tipi di risposta e i codici di errore, se diversi da quelli standard. I tipi di risposta e i codici di errore vengono indicati nei commenti XML e nelle annotazioni dei dati.

L'azione `Create` restituisce un codice di stato HTTP 201 in caso di esito positivo. Quando il corpo della richiesta inviata è Null, viene restituito un codice di stato HTTP 400. Senza la documentazione appropriata nell'interfaccia utente di Swagger, il consumer non riconosce tali risultati previsti. Risolvere il problema aggiungendo le righe evidenziate nell'esempio seguente:

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

L'interfaccia utente di Swagger ora documenta chiaramente i codici di risposta HTTP previsti:

![Interfaccia utente di Swagger che visualizza la descrizione della classe risposta POST "Returns the newly created Todo item" e "400 - If the item is null" per il codice di stato e il motivo nei messaggi di risposta](sample_images/data-annotations-response-types.png)

In ASP.NET Core 2.2 o versioni successive, possono essere usate convenzioni in alternativa alla decorazione esplicita di azioni singole con `[ProducesResponseType]`. Per altre informazioni, vedere [Usare le convenzioni dell'API Web](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).

Per informazioni sulla personalizzazione dell'interfaccia utente, vedere [personalizzare l'interfaccia utente](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)
