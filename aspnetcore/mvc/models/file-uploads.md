---
title: Caricare file in ASP.NET Core
author: rick-anderson
description: Come usare l'associazione del modello e lo streaming per caricare i file in ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2020
uid: mvc/models/file-uploads
ms.openlocfilehash: e25da0b3867181a16a4636768f36c148a152dd23
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661729"
---
# <a name="upload-files-in-aspnet-core"></a>Caricare file in ASP.NET Core

Di [Steve Smith](https://ardalis.com/) e [Rutger Storm](https://github.com/rutix)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core supporta il caricamento di uno o più file tramite l'associazione di modelli memorizzati nel buffer per file più piccoli e lo streaming senza buffer per file di dimensioni maggiori.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Prestare attenzione quando si fornisce agli utenti la possibilità di caricare file su un server. Gli aggressori possono tentare di:

* Eseguire attacchi [Denial of Service.](/windows-hardware/drivers/ifs/denial-of-service)
* Caricare virus o malware.
* Compromettere reti e server in altri modi.

I passaggi di sicurezza che riducono la probabilità di un attacco riuscito sono:

* Caricare i file in un'area di caricamento file dedicata, preferibilmente in un'unità non di sistema. Una posizione dedicata semplifica l'imponinza di restrizioni di sicurezza sui file caricati. Disabilitare le autorizzazioni di esecuzione per il percorso di caricamento del file.&dagger;
* **Non** rendere persistenti i file caricati nella stessa struttura di directory dell'app.&dagger;
* Usa un nome di file sicuro determinato dall'app. Non utilizzare un nome di file fornito dall'utente o il nome di file non attendibile del file caricato. &dagger; HTML codificare il nome del file non attendibile durante la visualizzazione. Ad esempio, la registrazione del nome del file o la visualizzazione nell'interfaccia utente (Razor codifica automaticamente l'output).
* Consenti solo le estensioni di file approvate per le specifiche di progettazione dell'app.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Verificare che i controlli sul lato client vengano eseguiti sul server. &dagger; I controlli lato client sono facili da aggirare.
* Controllare le dimensioni di un file caricato. Impostare un limite di dimensione massima per impedire caricamenti di grandi dimensioni.&dagger;
* Quando i file non devono essere sovrascritti da un file caricato con lo stesso nome, controllare il nome del file rispetto al database o all'archiviazione fisica prima di caricare il file.
* **Eseguire uno scanner antivirus/malware sul contenuto caricato prima che il file venga archiviato.**

&dagger;L'app di esempio illustra un approccio che soddisfa i criteri.

> [!WARNING]
> Il caricamento di codice dannoso in un sistema è spesso il primo passaggio per l'esecuzione di codice in grado di:
>
> * Ottenere completamente il controllo di un sistema.
> * Sovraccaricare un sistema con il risultato che il sistema si arresta in modo anomalo.
> * Compromettere i dati di sistemi o utenti.
> * Applicare graffiti a un'interfaccia utente pubblica.
>
> Per informazioni sulla riduzione della superficie di attacco quando si accettano file dagli utenti, vedere le risorse seguenti:
>
> * [Caricamento di file senza restrizioni](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Protezione di Azure: Verificare i controlli appropriati quando si accettano file dagli utenti](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Per altre informazioni sull'implementazione di misure di sicurezza, inclusi esempi dall'app di esempio, vedere la sezione [Convalida.For](#validation) more information on implementing security measures, including examples from the sample app, see the Validation section.

## <a name="storage-scenarios"></a>Scenari di archiviazioneStorage scenarios

Le opzioni di archiviazione comuni per i file includono:Common storage options for files include:

* Database

  * Per i caricamenti di file di piccole dimensioni, un database è spesso più veloce delle opzioni di archiviazione fisica (file system o condivisione di rete).
  * Un database è spesso più conveniente rispetto alle opzioni di archiviazione fisica perché il recupero di un record di database per i dati utente può fornire contemporaneamente il contenuto del file (ad esempio, un'immagine avatar).
  * Un database è potenzialmente meno costoso rispetto all'utilizzo di un servizio di archiviazione dati.

* Archiviazione fisica (file system o condivisione di rete)

  * Per i caricamenti di file di grandi dimensioni:
    * I limiti del database possono limitare le dimensioni del caricamento.
    * L'archiviazione fisica è spesso meno economica dell'archiviazione in un database.
  * L'archiviazione fisica è potenzialmente meno costosa rispetto all'utilizzo di un servizio di archiviazione dati.
  * Il processo dell'app deve disporre delle autorizzazioni di lettura e scrittura per il percorso di archiviazione. **Non concedere mai l'autorizzazione di esecuzione.**

* Servizio di archiviazione dati (ad esempio, Archiviazione BLOB di [Azure)](https://azure.microsoft.com/services/storage/blobs/)

  * I servizi offrono in genere una maggiore scalabilità e resilienza rispetto alle soluzioni locali che sono in genere soggette a singoli punti di errore.
  * I servizi sono potenzialmente più bassi in scenari di infrastruttura di archiviazione di grandi dimensioni.

  Per altre informazioni, vedere [Guida introduttiva: Usare .NET per creare un BLOB nell'archivio oggetti.](/azure/storage/blobs/storage-quickstart-blobs-dotnet)

## <a name="file-upload-scenarios"></a>Scenari di caricamento di file

Due approcci generali per il caricamento dei file sono il buffering e lo streaming.

**responseBuffering**

L'intero file viene <xref:Microsoft.AspNetCore.Http.IFormFile>letto in un oggetto , che è una rappresentazione in C, del file utilizzato per elaborare o salvare il file.

Le risorse (disco, memoria) utilizzate dai caricamenti di file dipendono dal numero e dalle dimensioni dei caricamenti simultanei di file. Se un'app tenta di memorizzare nel buffer troppi caricamenti, il sito si blocca quando si esaurisce la memoria o lo spazio su disco. Se le dimensioni o la frequenza dei caricamenti di file stanno esaurendo le risorse dell'app, usa lo streaming.

> [!NOTE]
> Qualsiasi singolo file memorizzato nel buffer superiore a 64 KB viene spostato dalla memoria a un file temporaneo su disco.

Il buffering di file di piccole dimensioni è illustrato nelle sezioni seguenti di questo argomento:

* [Archiviazione fisica](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Database](#upload-small-files-with-buffered-model-binding-to-a-database)

**Flusso**

Il file viene ricevuto da una richiesta multiparte ed elaborato direttamente o salvato dall'app. Lo streaming non migliora in modo significativo le prestazioni. Lo streaming riduce le richieste di memoria o spazio su disco durante il caricamento dei file.

Lo streaming di file di grandi dimensioni è illustrato nella sezione Caricare file di [grandi dimensioni con lo streaming.](#upload-large-files-with-streaming)

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer nell'archiviazione fisicaUpload small files with buffered model binding to physical storage

Per caricare file di piccole dimensioni, utilizzare un modulo multiparte o costruire una richiesta POST utilizzando JavaScript.

L'esempio seguente illustra l'uso di un modulo Pagine Razor per caricare un singolo file (*Pages/BufferedSingleFileUploadPhysical.cshtml* nell'app di esempio):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

L'esempio seguente è analogo all'esempio precedente, ad eccezione del fatto che:

* JavaScript ([API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API)) viene utilizzato per inviare i dati del modulo.
* Non c'è convalida.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Per eseguire il modulo POST in JavaScript per i client che [non supportano l'API Fetch](https://caniuse.com/#feat=fetch), utilizzare uno dei seguenti approcci:

* Utilizzare un polyfill Fetch (ad esempio, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Usare `XMLHttpRequest`. Ad esempio:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Per supportare i caricamenti di file, i`enctype`moduli `multipart/form-data`HTML devono specificare un tipo di codifica ( ) di .

Per `files` un elemento di input per `multiple` supportare `<input>` il caricamento di più file forniscono l'attributo sull'elemento:For a input element to support uploading multiple files provide the attribute on the element:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

È possibile accedere ai singoli file caricati <xref:Microsoft.AspNetCore.Http.IFormFile>sul server tramite [L'associazione di](xref:mvc/models/model-binding) modelli utilizzando . L'app di esempio illustra più caricamenti di file con buffer per scenari di archiviazione fisica e di database.

<a name="filename"></a>

> [!WARNING]
> **Non** utilizzare `FileName` la <xref:Microsoft.AspNetCore.Http.IFormFile> proprietà other than per la visualizzazione e la registrazione. Durante la visualizzazione o la registrazione, IL codice HTML codifica il nome del file. Un utente malintenzionato può fornire un nome file dannoso, inclusi i percorsi completi o relativi. Le domande devono:
>
> * Rimuovere il percorso dal nome file fornito dall'utente.
> * Salvare il nome del file con codifica HTML rimosso e rimosso dal percorso per l'interfaccia utente o la registrazione.
> * Generare un nuovo nome file casuale per l'archiviazione.
>
> Il codice seguente rimuove il percorso dal nome del file:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Gli esempi forniti finora non tengono conto delle considerazioni sulla sicurezza. Ulteriori informazioni vengono fornite dalle sezioni seguenti e [dall'app di esempio:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)
>
> * [Considerazioni relative alla sicurezza](#security-considerations)
> * [Validation](#validation)

Quando si caricano file <xref:Microsoft.AspNetCore.Http.IFormFile>utilizzando l'associazione di modelli e , il metodo di azione può accettare:

* Un <xref:Microsoft.AspNetCore.Http.IFormFile>singolo file .
* Una delle seguenti raccolte che rappresentano diversi file:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Elenco](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> L'associazione corrisponde ai file del modulo in base al nome. Ad esempio, `name` il `<input type="file" name="formFile">` valore HTML in deve corrispondere`FormFile`al parametro/proprietà di C, associato a ( ). Per altre informazioni, vedere la sezione Corrispondenza valore [dell'attributo Nome con nome del metodo POST.](#match-name-attribute-value-to-parameter-name-of-post-method)

L'esempio seguente:

* Scorre in ciclo uno o più file caricati.
* Utilizza [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) per restituire un percorso completo per un file, incluso il nome del file. 
* Salva i file nel file system locale utilizzando un nome di file generato dall'app.
* Restituisce il numero totale e le dimensioni dei file caricati.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Utilizzare `Path.GetRandomFileName` per generare un nome di file senza un percorso. Nell'esempio seguente, il percorso viene ottenuto dalla configurazione:In the following example, the path is obtained from configuration:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Il percorso passato <xref:System.IO.FileStream> all'oggetto *deve* includere il nome del file. Se il nome del file <xref:System.UnauthorizedAccessException> non viene fornito, viene generata un'eccezione in fase di esecuzione.

I file caricati utilizzando la <xref:Microsoft.AspNetCore.Http.IFormFile> tecnica vengono memorizzati nel buffer in memoria o su disco sul server prima dell'elaborazione. All'interno del <xref:Microsoft.AspNetCore.Http.IFormFile> metodo di azione, il contenuto è accessibile come <xref:System.IO.Stream>oggetto . Oltre al file system locale, i file possono essere salvati in una condivisione di rete o in un servizio di archiviazione file, ad esempio [Archiviazione BLOB](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)di Azure .

Per un altro esempio che esegue un ciclo su più file per il caricamento e usa nomi di file sicuri, vedere *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* nell'app di esempio.

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) genera <xref:System.IO.IOException> un'eccezione se vengono creati più di 65.535 file senza eliminare i file temporanei precedenti. Il limite di 65.535 file è un limite per server. Per ulteriori informazioni su questo limite nel sistema operativo Windows, vedere le osservazioni nei seguenti argomenti:
>
> * [GetTempFileNameA (funzione)](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer in un databaseUpload small files with buffered model binding to a database

Per archiviare i dati di file binari <xref:System.Byte> in un database utilizzando Entity [Framework](/ef/core/index), definire una proprietà di matrice nell'entità:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Specificare una proprietà del modello <xref:Microsoft.AspNetCore.Http.IFormFile>di pagina per la classe che include un:

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile>può essere utilizzato direttamente come parametro del metodo di azione o come proprietà del modello associato. Nell'esempio precedente viene utilizzata una proprietà del modello associato.

Viene `FileUpload` utilizzato nel modulo Pagine razor:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Quando il modulo è POSTed al <xref:Microsoft.AspNetCore.Http.IFormFile> server, copiare il in un flusso e salvarlo come matrice di byte nel database. Nell'esempio seguente `_dbContext` viene archiviato il contesto di database dell'app:In the following example, stores the app's database context:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

L'esempio precedente è simile a uno scenario illustrato nell'app di esempio:The preceding example is similar to a scenario demonstrated in the sample app:

* *Pagine/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Quando si archiviano dati binari all'interno di database relazionali, è necessario prestare attenzione, perché l'operazione può influire negativamente sulle prestazioni.
>
> Non fare affidamento o `FileName` considerare <xref:Microsoft.AspNetCore.Http.IFormFile> attendibile la proprietà di senza convalida. La `FileName` proprietà deve essere utilizzata solo per scopi di visualizzazione e solo dopo la codifica HTML.
>
> Gli esempi forniti non tengono conto delle considerazioni sulla sicurezza. Ulteriori informazioni vengono fornite dalle sezioni seguenti e [dall'app di esempio:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)
>
> * [Considerazioni relative alla sicurezza](#security-considerations)
> * [Validation](#validation)

### <a name="upload-large-files-with-streaming"></a>Caricare file di grandi dimensioni con lo streaming

Nell'esempio seguente viene illustrato come utilizzare JavaScript per trasmettere un file a un'azione del controller. Il token antifalandria del file viene generato utilizzando un attributo di filtro personalizzato e passato alle intestazioni HTTP del client anziché nel corpo della richiesta. Poiché il metodo di azione elabora direttamente i dati caricati, l'associazione del modello di modulo è disabilitata da un altro filtro personalizzato. All'interno dell'azione, il contenuto del form viene letto tramite un `MultipartReader`, che legge ogni singola `MultipartSection`, elaborando il file o archiviandone il contenuto, come appropriato. Dopo la lettura delle sezioni multiparte, l'azione esegue la propria associazione di modello.

La risposta iniziale della pagina carica il modulo e salva `GenerateAntiforgeryTokenCookieAttribute` un token antifalsificazione in un cookie (tramite l'attributo). L'attributo utilizza ASP.NET [supporto antifasciacquario](xref:security/anti-request-forgery) incorporato di Core per impostare un cookie con un token di richiesta:The attribute uses ASP.NET Core's built-in antiforgery support to set a cookie with a request token:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

L'oggetto viene utilizzato per disabilitare l'associazione di modelli:The `DisableFormValueModelBindingAttribute` is used to disable model binding:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

Nell'app di `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` esempio e vengono applicati come `/StreamedSingleFileUploadDb` filtri `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` ai modelli di applicazione pagina di e tramite [le convenzioni Razor Pages](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

Poiché l'associazione di modelli non legge il form, i parametri associati dal form non vengono associati (query, route e intestazione continuano a funzionare). Il metodo di azione `Request` funziona direttamente con la proprietà. Per leggere ogni sezione, viene usato un `MultipartReader`. I dati chiave/valore `KeyValueAccumulator`vengono memorizzati in un file . Dopo la lettura delle sezioni multiparte, il contenuto di `KeyValueAccumulator` vengono utilizzati per associare i dati del form a un tipo di modello.

Il `StreamingController.UploadDatabase` metodo completo per lo streaming in un database con EF Core:The complete method for streaming to a database with EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Utilità/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Il `StreamingController.UploadPhysical` metodo completo per lo streaming in una posizione fisica:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

Nell'app di esempio, i `FileHelpers.ProcessStreamedFile`controlli di convalida vengono gestiti da .

## <a name="validation"></a>Convalida

La classe dell'app di `FileHelpers` esempio illustra <xref:Microsoft.AspNetCore.Http.IFormFile> diversi controlli per i caricamenti di file memorizzati nel buffer e trasmessi tè. Per <xref:Microsoft.AspNetCore.Http.IFormFile> elaborare i caricamenti di file `ProcessFormFile` memorizzati nel buffer nell'app di esempio, vedere il metodo nel file *Utilities/FileHelpers.cs.For* processing buffered file uploads in the sample app, see the method in the Utilities/FileHelpers.cs file. Per l'elaborazione di `ProcessStreamedFile` file trasmessi, vedere il metodo nello stesso file.

> [!WARNING]
> I metodi di elaborazione della convalida illustrati nell'app di esempio non analizzano il contenuto dei file caricati. Nella maggior parte degli scenari di produzione, un'API dello scanner antivirus/malware viene utilizzata nel file prima di rendere il file disponibile agli utenti o ad altri sistemi.
>
> Anche se l'esempio di argomento fornisce un esempio `FileHelpers` funzionante di tecniche di convalida, non implementare la classe in un'app di produzione a meno che non si:Although the topic sample provides a working example of validation techniques, don't implement the class in a production app unless you:
>
> * Comprendere appieno l'implementazione.
> * Modificare l'implementazione in base all'ambiente e alle specifiche dell'app.
>
> **Non implementare mai indiscriminatamente il codice di sicurezza in un'app senza soddisfare questi requisiti.**

### <a name="content-validation"></a>Convalida contenuto

**Utilizzare un'API di scansione antivirus/malware di terze parti sul contenuto caricato.**

L'analisi dei file richiede risorse del server in scenari con volumi elevati. Se le prestazioni di elaborazione delle richieste diminuiscono a causa dell'analisi dei file, è consigliabile scaricare il lavoro di scansione in un [servizio in background,](xref:fundamentals/host/hosted-services)possibilmente un servizio in esecuzione su un server diverso dal server dell'app. In genere, i file caricati vengono mantenuti in un'area in quarantena fino a quando non vengono esaminati dall'utilità di analisi antivirus in background. Quando un file passa, il file viene spostato nel percorso di archiviazione del file normale. Questi passaggi vengono in genere eseguiti insieme a un record di database che indica lo stato di analisi di un file. Utilizzando tale approccio, l'app e il server applicazioni rimangono concentrati sulla risposta alle richieste.

### <a name="file-extension-validation"></a>Convalida dell'estensione file

L'estensione del file caricato deve essere confrontata con un elenco di estensioni consentite. Ad esempio:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Convalida della firma del file

La firma di un file è determinata dai primi byte all'inizio di un file. Questi byte possono essere utilizzati per indicare se l'estensione corrisponde al contenuto del file. L'app di esempio controlla le firme dei file per alcuni tipi di file comuni. Nell'esempio seguente, la firma del file per un'immagine JPEG viene confrontata con il file:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Per ottenere ulteriori firme di file, vedere il [Database delle firme](https://www.filesignatures.net/) dei file e le specifiche ufficiali dei file.

### <a name="file-name-security"></a>Sicurezza del nome file

Non utilizzare mai un nome di file fornito dal client per salvare un file nell'archivio fisico. Creare un nome di file sicuro per il file utilizzando [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) per creare un percorso completo (incluso il nome file) per l'archiviazione temporanea.

Razor codifica automaticamente i valori delle proprietà per la visualizzazione. Il codice seguente è sicuro da usare:The following code is safe to use:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Al di fuori <xref:System.Net.WebUtility.HtmlEncode*> di Razor, il contenuto del nome file da una richiesta dell'utente.

Molte implementazioni devono includere un controllo che il file esista; in caso contrario, il file viene sovrascritto da un file con lo stesso nome. Fornisci logica aggiuntiva per soddisfare le specifiche della tua app.

### <a name="size-validation"></a>Convalida delle dimensioni

Limitare le dimensioni dei file caricati.

Nell'app di esempio, la dimensione del file è limitata a 2 MB (indicata in byte). Il limite viene fornito tramite [Configuration](xref:fundamentals/configuration/index) dal file *appsettings.json:*

```json
{
  "FileSizeLimit": 2097152
}
```

Il `FileSizeLimit` viene iniettato nelle `PageModel` classi:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Quando una dimensione del file supera il limite, il file viene rifiutato:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Corrispondenza valore attributo nome al nome del metodo POST

Nei moduli non Razor che formano dati POST `FormData` o utilizzano direttamente JavaScript, `FormData` il nome specificato nell'elemento del modulo o deve corrispondere al nome del parametro nell'azione del controller.

Nell'esempio seguente:

* Quando si `<input>` utilizza `name` un elemento, l'attributo viene impostato sul valore `battlePlans`:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Quando `FormData` si utilizza in JavaScript, il `battlePlans`nome è impostato sul valore :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Utilizzare un nome corrispondente per il parametro del metodo C , (`battlePlans`):

* Per un metodo del gestore di pagina Razor Pages denominato `Upload`:

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Per un metodo di azione del controller MVC POST:For an MVC POST controller action method:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configurazione di server e app

### <a name="multipart-body-length-limit"></a>Limite di lunghezza del corpo multiparte

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>imposta il limite per la lunghezza di ogni corpo multiparte. Le sezioni del modulo <xref:System.IO.InvalidDataException> che superano questo limite generano un errore quando vengono analizzate. Il valore predefinito è 134.217.728 (128 MB). Personalizzare il limite <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> utilizzando `Startup.ConfigureServices`l'impostazione in:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>viene utilizzato per <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> impostare l'oggetto per una singola pagina o azione.

In un'app Razor Pages, applica `Startup.ConfigureServices`il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in:

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    });
```

In un'app Razor Pages o in un'app MVC applicare il filtro al modello di pagina o al metodo di azione:In a Razor Pages app or an MVC app, apply the filter to the page model or action method:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Dimensione massima del corpo della richiesta del gheccello

Per le app ospitate da Kestrel, la dimensione massima predefinita del corpo della richiesta è 30.000.000 byte, ovvero circa 28,6 MB. Personalizzare il limite utilizzando l'opzione server [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>viene utilizzato per impostare il [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) per una singola pagina o azione.

In un'app Razor Pages, applica `Startup.ConfigureServices`il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in:

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    });
```

In un'app di pagine Razor o in un'app MVC, applica il filtro alla classe del gestore di pagina o al metodo di azione:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

Il `RequestSizeLimitAttribute` può essere applicato [`@attribute`](xref:mvc/views/razor#attribute) anche utilizzando la direttiva Razor:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Altri limiti di Kestrel

Altri limiti di Kestrel possono richiedere le app ospitate da Kestrel:

* [Numero massimo di connessioni client](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Frequenza dei dati di richiesta e risposta](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>Limite di lunghezza del contenuto IIS

Il limite di`maxAllowedContentLength`richieste predefinito ( ) è 30.000.000 byte, ovvero circa 28,6 MB. Personalizzare il limite nel file *web.config:*

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Questa impostazione si applica solo a IIS. Il comportamento non si verifica per impostazione predefinita con l'hosting in Kestrel. Per ulteriori informazioni, vedere [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Limitazioni nel ASP.NET Core Module o la presenza del modulo filtro richieste IIS può limitare i caricamenti a 2 o 4 GB. Per ulteriori informazioni, vedere [Impossibile caricare file di dimensioni superiori a 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Risolvere problemi

Di seguito sono trattati alcuni problemi comuni riscontrati durante il caricamento di file, con le soluzioni possibili corrispondenti.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Errore Non trovato quando viene distribuito in un server IIS

Il seguente errore indica che il file caricato supera la lunghezza del contenuto configurato del server:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Per ulteriori informazioni sull'aumento del limite, vedere la sezione Limite di [lunghezza del contenuto di IIS.](#iis-content-length-limit)

### <a name="connection-failure"></a>Errore di connessione

Un errore di connessione e una connessione al server di reimpostazione indicaprobabilmente che il file caricato supera la dimensione massima del corpo della richiesta di Kestrel. Per altre informazioni, vedere la sezione [Dimensioni massime del corpo della richiesta di Kestrel.For](#kestrel-maximum-request-body-size) more information, see the Kestrel maximum request body size section. Anche i limiti di connessione del client Kestrel possono richiedere modifiche.

### <a name="null-reference-exception-with-iformfile"></a>Eccezione per riferimento Null con IFormFile

Se il controller accetta i <xref:Microsoft.AspNetCore.Http.IFormFile> file caricati utilizzando ma il valore `null` `enctype` è `multipart/form-data`, verificare che il modulo HTML specifichi un valore di . Se questo attributo non `<form>` è impostato sull'elemento, il caricamento <xref:Microsoft.AspNetCore.Http.IFormFile> del `null`file non viene eseguito e gli eventuali argomenti associati sono . Verificare inoltre che la denominazione del [caricamento nei dati del modulo corrisponda a quella dell'app.](#match-name-attribute-value-to-parameter-name-of-post-method)

### <a name="stream-was-too-long"></a>Flusso era troppo lungo

Gli esempi in questo <xref:System.IO.MemoryStream> argomento si basano su come mantenere il contenuto del file caricato. Il limite di `MemoryStream` `int.MaxValue`dimensione di a è . Se lo scenario di caricamento dei file dell'app richiede contenuto di file di dimensioni `MemoryStream` superiori a 50 MB, usa un approccio alternativo che non si basi su un singolo per contenere il contenuto di un file caricato.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core supporta il caricamento di uno o più file tramite l'associazione di modelli memorizzati nel buffer per file più piccoli e lo streaming senza buffer per file di dimensioni maggiori.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Prestare attenzione quando si fornisce agli utenti la possibilità di caricare file su un server. Gli aggressori possono tentare di:

* Eseguire attacchi [Denial of Service.](/windows-hardware/drivers/ifs/denial-of-service)
* Caricare virus o malware.
* Compromettere reti e server in altri modi.

I passaggi di sicurezza che riducono la probabilità di un attacco riuscito sono:

* Caricare i file in un'area di caricamento file dedicata, preferibilmente in un'unità non di sistema. Una posizione dedicata semplifica l'imponinza di restrizioni di sicurezza sui file caricati. Disabilitare le autorizzazioni di esecuzione per il percorso di caricamento del file.&dagger;
* **Non** rendere persistenti i file caricati nella stessa struttura di directory dell'app.&dagger;
* Usa un nome di file sicuro determinato dall'app. Non utilizzare un nome di file fornito dall'utente o il nome di file non attendibile del file caricato. &dagger; HTML codificare il nome del file non attendibile durante la visualizzazione. Ad esempio, la registrazione del nome del file o la visualizzazione nell'interfaccia utente (Razor codifica automaticamente l'output).
* Consenti solo le estensioni di file approvate per le specifiche di progettazione dell'app.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Verificare che i controlli sul lato client vengano eseguiti sul server. &dagger; I controlli lato client sono facili da aggirare.
* Controllare le dimensioni di un file caricato. Impostare un limite di dimensione massima per impedire caricamenti di grandi dimensioni.&dagger;
* Quando i file non devono essere sovrascritti da un file caricato con lo stesso nome, controllare il nome del file rispetto al database o all'archiviazione fisica prima di caricare il file.
* **Eseguire uno scanner antivirus/malware sul contenuto caricato prima che il file venga archiviato.**

&dagger;L'app di esempio illustra un approccio che soddisfa i criteri.

> [!WARNING]
> Il caricamento di codice dannoso in un sistema è spesso il primo passaggio per l'esecuzione di codice in grado di:
>
> * Ottenere completamente il controllo di un sistema.
> * Sovraccaricare un sistema con il risultato che il sistema si arresta in modo anomalo.
> * Compromettere i dati di sistemi o utenti.
> * Applicare graffiti a un'interfaccia utente pubblica.
>
> Per informazioni sulla riduzione della superficie di attacco quando si accettano file dagli utenti, vedere le risorse seguenti:
>
> * [Caricamento di file senza restrizioni](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Protezione di Azure: Verificare i controlli appropriati quando si accettano file dagli utenti](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Per altre informazioni sull'implementazione di misure di sicurezza, inclusi esempi dall'app di esempio, vedere la sezione [Convalida.For](#validation) more information on implementing security measures, including examples from the sample app, see the Validation section.

## <a name="storage-scenarios"></a>Scenari di archiviazioneStorage scenarios

Le opzioni di archiviazione comuni per i file includono:Common storage options for files include:

* Database

  * Per i caricamenti di file di piccole dimensioni, un database è spesso più veloce delle opzioni di archiviazione fisica (file system o condivisione di rete).
  * Un database è spesso più conveniente rispetto alle opzioni di archiviazione fisica perché il recupero di un record di database per i dati utente può fornire contemporaneamente il contenuto del file (ad esempio, un'immagine avatar).
  * Un database è potenzialmente meno costoso rispetto all'utilizzo di un servizio di archiviazione dati.

* Archiviazione fisica (file system o condivisione di rete)

  * Per i caricamenti di file di grandi dimensioni:
    * I limiti del database possono limitare le dimensioni del caricamento.
    * L'archiviazione fisica è spesso meno economica dell'archiviazione in un database.
  * L'archiviazione fisica è potenzialmente meno costosa rispetto all'utilizzo di un servizio di archiviazione dati.
  * Il processo dell'app deve disporre delle autorizzazioni di lettura e scrittura per il percorso di archiviazione. **Non concedere mai l'autorizzazione di esecuzione.**

* Servizio di archiviazione dati (ad esempio, Archiviazione BLOB di [Azure)](https://azure.microsoft.com/services/storage/blobs/)

  * I servizi offrono in genere una maggiore scalabilità e resilienza rispetto alle soluzioni locali che sono in genere soggette a singoli punti di errore.
  * I servizi sono potenzialmente più bassi in scenari di infrastruttura di archiviazione di grandi dimensioni.

  Per altre informazioni, vedere [Guida introduttiva: Usare .NET per creare un BLOB nell'archivio oggetti.](/azure/storage/blobs/storage-quickstart-blobs-dotnet) L'argomento <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>illustra <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> , ma può <xref:System.IO.FileStream> essere usato per <xref:System.IO.Stream>salvare un archivio BLOB quando si lavora con un oggetto .

## <a name="file-upload-scenarios"></a>Scenari di caricamento di file

Due approcci generali per il caricamento dei file sono il buffering e lo streaming.

**responseBuffering**

L'intero file viene <xref:Microsoft.AspNetCore.Http.IFormFile>letto in un oggetto , che è una rappresentazione in C, del file utilizzato per elaborare o salvare il file.

Le risorse (disco, memoria) utilizzate dai caricamenti di file dipendono dal numero e dalle dimensioni dei caricamenti simultanei di file. Se un'app tenta di memorizzare nel buffer troppi caricamenti, il sito si blocca quando si esaurisce la memoria o lo spazio su disco. Se le dimensioni o la frequenza dei caricamenti di file stanno esaurendo le risorse dell'app, usa lo streaming.

> [!NOTE]
> Qualsiasi singolo file memorizzato nel buffer superiore a 64 KB viene spostato dalla memoria a un file temporaneo su disco.

Il buffering di file di piccole dimensioni è illustrato nelle sezioni seguenti di questo argomento:

* [Archiviazione fisica](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Database](#upload-small-files-with-buffered-model-binding-to-a-database)

**Flusso**

Il file viene ricevuto da una richiesta multiparte ed elaborato direttamente o salvato dall'app. Lo streaming non migliora in modo significativo le prestazioni. Lo streaming riduce le richieste di memoria o spazio su disco durante il caricamento dei file.

Lo streaming di file di grandi dimensioni è illustrato nella sezione Caricare file di [grandi dimensioni con lo streaming.](#upload-large-files-with-streaming)

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer nell'archiviazione fisicaUpload small files with buffered model binding to physical storage

Per caricare file di piccole dimensioni, utilizzare un modulo multiparte o costruire una richiesta POST utilizzando JavaScript.

L'esempio seguente illustra l'uso di un modulo Pagine Razor per caricare un singolo file (*Pages/BufferedSingleFileUploadPhysical.cshtml* nell'app di esempio):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

L'esempio seguente è analogo all'esempio precedente, ad eccezione del fatto che:

* JavaScript ([API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API)) viene utilizzato per inviare i dati del modulo.
* Non c'è convalida.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Per eseguire il modulo POST in JavaScript per i client che [non supportano l'API Fetch](https://caniuse.com/#feat=fetch), utilizzare uno dei seguenti approcci:

* Utilizzare un polyfill Fetch (ad esempio, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Usare `XMLHttpRequest`. Ad esempio:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Per supportare i caricamenti di file, i`enctype`moduli `multipart/form-data`HTML devono specificare un tipo di codifica ( ) di .

Per `files` un elemento di input per `multiple` supportare `<input>` il caricamento di più file forniscono l'attributo sull'elemento:For a input element to support uploading multiple files provide the attribute on the element:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

È possibile accedere ai singoli file caricati <xref:Microsoft.AspNetCore.Http.IFormFile>sul server tramite [L'associazione di](xref:mvc/models/model-binding) modelli utilizzando . L'app di esempio illustra più caricamenti di file con buffer per scenari di archiviazione fisica e di database.

<a name="filename2"></a>

> [!WARNING]
> **Non** utilizzare `FileName` la <xref:Microsoft.AspNetCore.Http.IFormFile> proprietà other than per la visualizzazione e la registrazione. Durante la visualizzazione o la registrazione, IL codice HTML codifica il nome del file. Un utente malintenzionato può fornire un nome file dannoso, inclusi i percorsi completi o relativi. Le domande devono:
>
> * Rimuovere il percorso dal nome file fornito dall'utente.
> * Salvare il nome del file con codifica HTML rimosso e rimosso dal percorso per l'interfaccia utente o la registrazione.
> * Generare un nuovo nome file casuale per l'archiviazione.
>
> Il codice seguente rimuove il percorso dal nome del file:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Gli esempi forniti finora non tengono conto delle considerazioni sulla sicurezza. Ulteriori informazioni vengono fornite dalle sezioni seguenti e [dall'app di esempio:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)
>
> * [Considerazioni relative alla sicurezza](#security-considerations)
> * [Validation](#validation)

Quando si caricano file <xref:Microsoft.AspNetCore.Http.IFormFile>utilizzando l'associazione di modelli e , il metodo di azione può accettare:

* Un <xref:Microsoft.AspNetCore.Http.IFormFile>singolo file .
* Una delle seguenti raccolte che rappresentano diversi file:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Elenco](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> L'associazione corrisponde ai file del modulo in base al nome. Ad esempio, `name` il `<input type="file" name="formFile">` valore HTML in deve corrispondere`FormFile`al parametro/proprietà di C, associato a ( ). Per altre informazioni, vedere la sezione Corrispondenza valore [dell'attributo Nome con nome del metodo POST.](#match-name-attribute-value-to-parameter-name-of-post-method)

L'esempio seguente:

* Scorre in ciclo uno o più file caricati.
* Utilizza [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) per restituire un percorso completo per un file, incluso il nome del file. 
* Salva i file nel file system locale utilizzando un nome di file generato dall'app.
* Restituisce il numero totale e le dimensioni dei file caricati.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Utilizzare `Path.GetRandomFileName` per generare un nome di file senza un percorso. Nell'esempio seguente, il percorso viene ottenuto dalla configurazione:In the following example, the path is obtained from configuration:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Il percorso passato <xref:System.IO.FileStream> all'oggetto *deve* includere il nome del file. Se il nome del file <xref:System.UnauthorizedAccessException> non viene fornito, viene generata un'eccezione in fase di esecuzione.

I file caricati utilizzando la <xref:Microsoft.AspNetCore.Http.IFormFile> tecnica vengono memorizzati nel buffer in memoria o su disco sul server prima dell'elaborazione. All'interno del <xref:Microsoft.AspNetCore.Http.IFormFile> metodo di azione, il contenuto è accessibile come <xref:System.IO.Stream>oggetto . Oltre al file system locale, i file possono essere salvati in una condivisione di rete o in un servizio di archiviazione file, ad esempio [Archiviazione BLOB](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)di Azure .

Per un altro esempio che esegue un ciclo su più file per il caricamento e usa nomi di file sicuri, vedere *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* nell'app di esempio.

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) genera <xref:System.IO.IOException> un'eccezione se vengono creati più di 65.535 file senza eliminare i file temporanei precedenti. Il limite di 65.535 file è un limite per server. Per ulteriori informazioni su questo limite nel sistema operativo Windows, vedere le osservazioni nei seguenti argomenti:
>
> * [GetTempFileNameA (funzione)](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer in un databaseUpload small files with buffered model binding to a database

Per archiviare i dati di file binari <xref:System.Byte> in un database utilizzando Entity [Framework](/ef/core/index), definire una proprietà di matrice nell'entità:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Specificare una proprietà del modello <xref:Microsoft.AspNetCore.Http.IFormFile>di pagina per la classe che include un:

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile>può essere utilizzato direttamente come parametro del metodo di azione o come proprietà del modello associato. Nell'esempio precedente viene utilizzata una proprietà del modello associato.

Viene `FileUpload` utilizzato nel modulo Pagine razor:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Quando il modulo è POSTed al <xref:Microsoft.AspNetCore.Http.IFormFile> server, copiare il in un flusso e salvarlo come matrice di byte nel database. Nell'esempio seguente `_dbContext` viene archiviato il contesto di database dell'app:In the following example, stores the app's database context:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

L'esempio precedente è simile a uno scenario illustrato nell'app di esempio:The preceding example is similar to a scenario demonstrated in the sample app:

* *Pagine/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Quando si archiviano dati binari all'interno di database relazionali, è necessario prestare attenzione, perché l'operazione può influire negativamente sulle prestazioni.
>
> Non fare affidamento o `FileName` considerare <xref:Microsoft.AspNetCore.Http.IFormFile> attendibile la proprietà di senza convalida. La `FileName` proprietà deve essere utilizzata solo per scopi di visualizzazione e solo dopo la codifica HTML.
>
> Gli esempi forniti non tengono conto delle considerazioni sulla sicurezza. Ulteriori informazioni vengono fornite dalle sezioni seguenti e [dall'app di esempio:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)
>
> * [Considerazioni relative alla sicurezza](#security-considerations)
> * [Validation](#validation)

### <a name="upload-large-files-with-streaming"></a>Caricare file di grandi dimensioni con lo streaming

Nell'esempio seguente viene illustrato come utilizzare JavaScript per trasmettere un file a un'azione del controller. Il token antifalandria del file viene generato utilizzando un attributo di filtro personalizzato e passato alle intestazioni HTTP del client anziché nel corpo della richiesta. Poiché il metodo di azione elabora direttamente i dati caricati, l'associazione del modello di modulo è disabilitata da un altro filtro personalizzato. All'interno dell'azione, il contenuto del form viene letto tramite un `MultipartReader`, che legge ogni singola `MultipartSection`, elaborando il file o archiviandone il contenuto, come appropriato. Dopo la lettura delle sezioni multiparte, l'azione esegue la propria associazione di modello.

La risposta iniziale della pagina carica il modulo e salva `GenerateAntiforgeryTokenCookieAttribute` un token antifalsificazione in un cookie (tramite l'attributo). L'attributo utilizza ASP.NET [supporto antifasciacquario](xref:security/anti-request-forgery) incorporato di Core per impostare un cookie con un token di richiesta:The attribute uses ASP.NET Core's built-in antiforgery support to set a cookie with a request token:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

L'oggetto viene utilizzato per disabilitare l'associazione di modelli:The `DisableFormValueModelBindingAttribute` is used to disable model binding:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

Nell'app di `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` esempio e vengono applicati come `/StreamedSingleFileUploadDb` filtri `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` ai modelli di applicazione pagina di e tramite [le convenzioni Razor Pages](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Poiché l'associazione di modelli non legge il form, i parametri associati dal form non vengono associati (query, route e intestazione continuano a funzionare). Il metodo di azione `Request` funziona direttamente con la proprietà. Per leggere ogni sezione, viene usato un `MultipartReader`. I dati chiave/valore `KeyValueAccumulator`vengono memorizzati in un file . Dopo la lettura delle sezioni multiparte, il contenuto di `KeyValueAccumulator` vengono utilizzati per associare i dati del form a un tipo di modello.

Il `StreamingController.UploadDatabase` metodo completo per lo streaming in un database con EF Core:The complete method for streaming to a database with EF Core:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Utilità/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Il `StreamingController.UploadPhysical` metodo completo per lo streaming in una posizione fisica:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

Nell'app di esempio, i `FileHelpers.ProcessStreamedFile`controlli di convalida vengono gestiti da .

## <a name="validation"></a>Convalida

La classe dell'app di `FileHelpers` esempio illustra <xref:Microsoft.AspNetCore.Http.IFormFile> diversi controlli per i caricamenti di file memorizzati nel buffer e trasmessi tè. Per <xref:Microsoft.AspNetCore.Http.IFormFile> elaborare i caricamenti di file `ProcessFormFile` memorizzati nel buffer nell'app di esempio, vedere il metodo nel file *Utilities/FileHelpers.cs.For* processing buffered file uploads in the sample app, see the method in the Utilities/FileHelpers.cs file. Per l'elaborazione di `ProcessStreamedFile` file trasmessi, vedere il metodo nello stesso file.

> [!WARNING]
> I metodi di elaborazione della convalida illustrati nell'app di esempio non analizzano il contenuto dei file caricati. Nella maggior parte degli scenari di produzione, un'API dello scanner antivirus/malware viene utilizzata nel file prima di rendere il file disponibile agli utenti o ad altri sistemi.
>
> Anche se l'esempio di argomento fornisce un esempio `FileHelpers` funzionante di tecniche di convalida, non implementare la classe in un'app di produzione a meno che non si:Although the topic sample provides a working example of validation techniques, don't implement the class in a production app unless you:
>
> * Comprendere appieno l'implementazione.
> * Modificare l'implementazione in base all'ambiente e alle specifiche dell'app.
>
> **Non implementare mai indiscriminatamente il codice di sicurezza in un'app senza soddisfare questi requisiti.**

### <a name="content-validation"></a>Convalida contenuto

**Utilizzare un'API di scansione antivirus/malware di terze parti sul contenuto caricato.**

L'analisi dei file richiede risorse del server in scenari con volumi elevati. Se le prestazioni di elaborazione delle richieste diminuiscono a causa dell'analisi dei file, è consigliabile scaricare il lavoro di scansione in un [servizio in background,](xref:fundamentals/host/hosted-services)possibilmente un servizio in esecuzione su un server diverso dal server dell'app. In genere, i file caricati vengono mantenuti in un'area in quarantena fino a quando non vengono esaminati dall'utilità di analisi antivirus in background. Quando un file passa, il file viene spostato nel percorso di archiviazione del file normale. Questi passaggi vengono in genere eseguiti insieme a un record di database che indica lo stato di analisi di un file. Utilizzando tale approccio, l'app e il server applicazioni rimangono concentrati sulla risposta alle richieste.

### <a name="file-extension-validation"></a>Convalida dell'estensione file

L'estensione del file caricato deve essere confrontata con un elenco di estensioni consentite. Ad esempio:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Convalida della firma del file

La firma di un file è determinata dai primi byte all'inizio di un file. Questi byte possono essere utilizzati per indicare se l'estensione corrisponde al contenuto del file. L'app di esempio controlla le firme dei file per alcuni tipi di file comuni. Nell'esempio seguente, la firma del file per un'immagine JPEG viene confrontata con il file:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Per ottenere ulteriori firme di file, vedere il [Database delle firme](https://www.filesignatures.net/) dei file e le specifiche ufficiali dei file.

### <a name="file-name-security"></a>Sicurezza del nome file

Non utilizzare mai un nome di file fornito dal client per salvare un file nell'archivio fisico. Creare un nome di file sicuro per il file utilizzando [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) per creare un percorso completo (incluso il nome file) per l'archiviazione temporanea.

Razor codifica automaticamente i valori delle proprietà per la visualizzazione. Il codice seguente è sicuro da usare:The following code is safe to use:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Al di fuori <xref:System.Net.WebUtility.HtmlEncode*> di Razor, il contenuto del nome file da una richiesta dell'utente.

Molte implementazioni devono includere un controllo che il file esista; in caso contrario, il file viene sovrascritto da un file con lo stesso nome. Fornisci logica aggiuntiva per soddisfare le specifiche della tua app.

### <a name="size-validation"></a>Convalida delle dimensioni

Limitare le dimensioni dei file caricati.

Nell'app di esempio, la dimensione del file è limitata a 2 MB (indicata in byte). Il limite viene fornito tramite [Configuration](xref:fundamentals/configuration/index) dal file *appsettings.json:*

```json
{
  "FileSizeLimit": 2097152
}
```

Il `FileSizeLimit` viene iniettato nelle `PageModel` classi:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Quando una dimensione del file supera il limite, il file viene rifiutato:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Corrispondenza valore attributo nome al nome del metodo POST

Nei moduli non Razor che formano dati POST `FormData` o utilizzano direttamente JavaScript, `FormData` il nome specificato nell'elemento del modulo o deve corrispondere al nome del parametro nell'azione del controller.

Nell'esempio seguente:

* Quando si `<input>` utilizza `name` un elemento, l'attributo viene impostato sul valore `battlePlans`:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Quando `FormData` si utilizza in JavaScript, il `battlePlans`nome è impostato sul valore :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Utilizzare un nome corrispondente per il parametro del metodo C , (`battlePlans`):

* Per un metodo del gestore di pagina Razor Pages denominato `Upload`:

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Per un metodo di azione del controller MVC POST:For an MVC POST controller action method:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configurazione di server e app

### <a name="multipart-body-length-limit"></a>Limite di lunghezza del corpo multiparte

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>imposta il limite per la lunghezza di ogni corpo multiparte. Le sezioni del modulo <xref:System.IO.InvalidDataException> che superano questo limite generano un errore quando vengono analizzate. Il valore predefinito è 134.217.728 (128 MB). Personalizzare il limite <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> utilizzando `Startup.ConfigureServices`l'impostazione in:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>viene utilizzato per <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> impostare l'oggetto per una singola pagina o azione.

In un'app Razor Pages, applica `Startup.ConfigureServices`il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

In un'app Razor Pages o in un'app MVC applicare il filtro al modello di pagina o al metodo di azione:In a Razor Pages app or an MVC app, apply the filter to the page model or action method:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Dimensione massima del corpo della richiesta del gheccello

Per le app ospitate da Kestrel, la dimensione massima predefinita del corpo della richiesta è 30.000.000 byte, ovvero circa 28,6 MB. Personalizzare il limite utilizzando l'opzione server [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>viene utilizzato per impostare il [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) per una singola pagina o azione.

In un'app Razor Pages, applica `Startup.ConfigureServices`il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

In un'app di pagine Razor o in un'app MVC, applica il filtro alla classe del gestore di pagina o al metodo di azione:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Altri limiti di Kestrel

Altri limiti di Kestrel possono richiedere le app ospitate da Kestrel:

* [Numero massimo di connessioni client](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Frequenza dei dati di richiesta e risposta](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>Limite di lunghezza del contenuto IIS

Il limite di`maxAllowedContentLength`richieste predefinito ( ) è 30.000.000 byte, ovvero circa 28,6 MB. Personalizzare il limite nel file *web.config:*

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Questa impostazione si applica solo a IIS. Il comportamento non si verifica per impostazione predefinita con l'hosting in Kestrel. Per ulteriori informazioni, vedere [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Limitazioni nel ASP.NET Core Module o la presenza del modulo filtro richieste IIS può limitare i caricamenti a 2 o 4 GB. Per ulteriori informazioni, vedere [Impossibile caricare file di dimensioni superiori a 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Risolvere problemi

Di seguito sono trattati alcuni problemi comuni riscontrati durante il caricamento di file, con le soluzioni possibili corrispondenti.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Errore Non trovato quando viene distribuito in un server IIS

Il seguente errore indica che il file caricato supera la lunghezza del contenuto configurato del server:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Per ulteriori informazioni sull'aumento del limite, vedere la sezione Limite di [lunghezza del contenuto di IIS.](#iis-content-length-limit)

### <a name="connection-failure"></a>Errore di connessione

Un errore di connessione e una connessione al server di reimpostazione indicaprobabilmente che il file caricato supera la dimensione massima del corpo della richiesta di Kestrel. Per altre informazioni, vedere la sezione [Dimensioni massime del corpo della richiesta di Kestrel.For](#kestrel-maximum-request-body-size) more information, see the Kestrel maximum request body size section. Anche i limiti di connessione del client Kestrel possono richiedere modifiche.

### <a name="null-reference-exception-with-iformfile"></a>Eccezione per riferimento Null con IFormFile

Se il controller accetta i <xref:Microsoft.AspNetCore.Http.IFormFile> file caricati utilizzando ma il valore `null` `enctype` è `multipart/form-data`, verificare che il modulo HTML specifichi un valore di . Se questo attributo non `<form>` è impostato sull'elemento, il caricamento <xref:Microsoft.AspNetCore.Http.IFormFile> del `null`file non viene eseguito e gli eventuali argomenti associati sono . Verificare inoltre che la denominazione del [caricamento nei dati del modulo corrisponda a quella dell'app.](#match-name-attribute-value-to-parameter-name-of-post-method)

### <a name="stream-was-too-long"></a>Flusso era troppo lungo

Gli esempi in questo <xref:System.IO.MemoryStream> argomento si basano su come mantenere il contenuto del file caricato. Il limite di `MemoryStream` `int.MaxValue`dimensione di a è . Se lo scenario di caricamento dei file dell'app richiede contenuto di file di dimensioni `MemoryStream` superiori a 50 MB, usa un approccio alternativo che non si basi su un singolo per contenere il contenuto di un file caricato.

::: moniker-end


## <a name="additional-resources"></a>Risorse aggiuntive

* [Caricamento di file senza restrizioni](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [Sicurezza di Azure: Frame di sicurezza: Convalida dell'input Mitigazioni](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Modelli di progettazione cloud di Azure: modello di chiave valetAzure Cloud Design Patterns: Valet Key pattern](/azure/architecture/patterns/valet-key)
