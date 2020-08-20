---
title: Caricare file in ASP.NET Core
author: rick-anderson
description: Come usare l'associazione del modello e lo streaming per caricare i file in ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/03/2020
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
uid: mvc/models/file-uploads
ms.openlocfilehash: 93ffa3a5313e63a1e9b98fb5bf9788944254213f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635216"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="ba8bc-103">Caricare file in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ba8bc-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="ba8bc-104">Di [Steve Smith](https://ardalis.com/) e [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="ba8bc-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba8bc-105">ASP.NET Core supporta il caricamento di uno o più file mediante l'associazione di modelli memorizzati nel buffer per i file più piccoli e il flusso non memorizzato nel buffer per file di dimensioni maggiori.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="ba8bc-106">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ba8bc-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="ba8bc-107">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="ba8bc-107">Security considerations</span></span>

<span data-ttu-id="ba8bc-108">Prestare attenzione quando si fornisce agli utenti la possibilità di caricare file in un server.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="ba8bc-109">Gli utenti malintenzionati possono tentare di:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="ba8bc-110">Eseguire attacchi [di tipo Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="ba8bc-111">Caricare virus o malware.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="ba8bc-112">Compromettere le reti e i server in altri modi.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="ba8bc-113">I passaggi di sicurezza che riducono la probabilità di un attacco riuscito sono:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="ba8bc-114">Caricare i file in un'area di caricamento file dedicata, preferibilmente in un'unità non di sistema.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="ba8bc-115">Un percorso dedicato semplifica l'imposizione delle restrizioni di sicurezza sui file caricati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="ba8bc-116">Disabilitare le autorizzazioni di esecuzione per il percorso di caricamento del file.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba8bc-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="ba8bc-117">Non **rende permanente i** file caricati nella stessa struttura di directory dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba8bc-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="ba8bc-118">Usare un nome file sicuro determinato dall'app.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="ba8bc-119">Non usare un nome file fornito dall'utente o il nome file non attendibile del file caricato. &dagger; HTML codifica il nome file non attendibile quando viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="ba8bc-120">Ad esempio, registrare il nome del file o visualizzarlo nell'interfaccia utente (l' Razor output viene codificato automaticamente in HTML).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="ba8bc-121">Consenti solo le estensioni di file approvate per la specifica di progettazione dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba8bc-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="ba8bc-122">Verificare che i controlli lato client vengano eseguiti sul server. &dagger; I controlli lato client sono facili da aggirare.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="ba8bc-123">Controllare le dimensioni di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="ba8bc-124">Impostare un limite di dimensioni massime per impedire caricamenti di grandi dimensioni.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba8bc-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="ba8bc-125">Quando i file non devono essere sovrascritti da un file caricato con lo stesso nome, controllare il nome del file sul database o sull'archiviazione fisica prima di caricare il file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="ba8bc-126">**Eseguire uno scanner virus/malware sul contenuto caricato prima che il file venga archiviato.**</span><span class="sxs-lookup"><span data-stu-id="ba8bc-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="ba8bc-127">&dagger;L'app di esempio illustra un approccio che soddisfa i criteri.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="ba8bc-128">Il caricamento di codice dannoso in un sistema è spesso il primo passaggio per l'esecuzione di codice in grado di:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="ba8bc-129">Ottenere il controllo completo di un sistema.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="ba8bc-130">Sovraccaricare un sistema con il risultato che si verifica un arresto anomalo del sistema.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="ba8bc-131">Compromettere i dati di sistemi o utenti.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="ba8bc-132">Applicare i graffiti a un'interfaccia utente pubblica.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="ba8bc-133">Per informazioni sulla riduzione della superficie di attacco quando si accettano file dagli utenti, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="ba8bc-134">Caricamento di file senza restrizioni</span><span class="sxs-lookup"><span data-stu-id="ba8bc-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="ba8bc-135">Protezione di Azure: Verificare i controlli appropriati quando si accettano file dagli utenti</span><span class="sxs-lookup"><span data-stu-id="ba8bc-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="ba8bc-136">Per ulteriori informazioni sull'implementazione di misure di sicurezza, inclusi esempi dall'app di esempio, vedere la sezione [convalida](#validation) .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="ba8bc-137">Scenari di archiviazione</span><span class="sxs-lookup"><span data-stu-id="ba8bc-137">Storage scenarios</span></span>

<span data-ttu-id="ba8bc-138">Le opzioni di archiviazione comuni per i file includono:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-138">Common storage options for files include:</span></span>

* <span data-ttu-id="ba8bc-139">Database</span><span class="sxs-lookup"><span data-stu-id="ba8bc-139">Database</span></span>

  * <span data-ttu-id="ba8bc-140">Per i caricamenti di file di piccole dimensioni, un database è spesso più veloce rispetto alle opzioni di archiviazione fisica (file system o condivisione di rete).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="ba8bc-141">Un database è spesso più pratico delle opzioni di archiviazione fisica perché il recupero di un record di database per i dati utente può fornire simultaneamente il contenuto del file, ad esempio un'immagine avatar.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="ba8bc-142">Un database è potenzialmente meno costoso rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="ba8bc-143">Archiviazione fisica (file system o condivisione di rete)</span><span class="sxs-lookup"><span data-stu-id="ba8bc-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="ba8bc-144">Per i caricamenti di file di grandi dimensioni:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-144">For large file uploads:</span></span>
    * <span data-ttu-id="ba8bc-145">I limiti del database possono limitare le dimensioni del caricamento.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="ba8bc-146">L'archiviazione fisica è spesso meno economica rispetto all'archiviazione in un database.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="ba8bc-147">L'archiviazione fisica è potenzialmente meno costosa rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="ba8bc-148">Il processo dell'app deve avere le autorizzazioni di lettura e scrittura per il percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="ba8bc-149">**Non concedere mai l'autorizzazione Execute.**</span><span class="sxs-lookup"><span data-stu-id="ba8bc-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="ba8bc-150">Servizio di archiviazione dei dati (ad esempio, [archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="ba8bc-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="ba8bc-151">I servizi in genere offrono una maggiore scalabilità e resilienza rispetto a soluzioni locali che in genere sono soggette a singoli punti di errore.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="ba8bc-152">I servizi sono un costo potenzialmente inferiore in scenari di infrastruttura di archiviazione di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="ba8bc-153">Per altre informazioni, vedere [Guida introduttiva: usare .NET per creare un BLOB nell'archivio oggetti](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="ba8bc-154">Scenari di caricamento di file</span><span class="sxs-lookup"><span data-stu-id="ba8bc-154">File upload scenarios</span></span>

<span data-ttu-id="ba8bc-155">Due approcci generali per il caricamento di file sono il buffering e il flusso.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="ba8bc-156">**responseBuffering**</span><span class="sxs-lookup"><span data-stu-id="ba8bc-156">**Buffering**</span></span>

<span data-ttu-id="ba8bc-157">L'intero file viene letto in un oggetto <xref:Microsoft.AspNetCore.Http.IFormFile> , ovvero una rappresentazione C# del file usato per elaborare o salvare il file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="ba8bc-158">Le risorse (disco, memoria) utilizzate dai caricamenti di file dipendono dal numero e dalle dimensioni dei caricamenti di file simultanei.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="ba8bc-159">Se un'app tenta di memorizzare nel buffer un numero eccessivo di caricamenti, il sito si arresta in modo anomalo quando esaurisce la memoria o lo spazio su disco.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="ba8bc-160">Se le dimensioni o la frequenza dei caricamenti di file esauriscono le risorse dell'app, usare il flusso.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="ba8bc-161">Ogni singolo file memorizzato nel buffer che supera 64 KB viene spostato dalla memoria in un file temporaneo su disco.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="ba8bc-162">Il buffering di file di piccole dimensioni viene trattato nelle sezioni seguenti di questo argomento:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="ba8bc-163">Archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="ba8bc-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="ba8bc-164">Database</span><span class="sxs-lookup"><span data-stu-id="ba8bc-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="ba8bc-165">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="ba8bc-165">**Streaming**</span></span>

<span data-ttu-id="ba8bc-166">Il file viene ricevuto da una richiesta multipart ed elaborato o salvato direttamente dall'app.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="ba8bc-167">Il flusso non migliora significativamente le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="ba8bc-168">Il flusso riduce le richieste di memoria o di spazio su disco durante il caricamento dei file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="ba8bc-169">Il flusso di file di grandi dimensioni è trattato nella sezione [caricare file di grandi dimensioni con flusso](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="ba8bc-170">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer all'archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="ba8bc-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="ba8bc-171">Per caricare file di piccole dimensioni, usare un modulo multipart o creare una richiesta POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="ba8bc-172">L'esempio seguente illustra l'uso di un Razor form di pagine per caricare un singolo file (*pages/BufferedSingleFileUploadPhysical. cshtml* nell'app di esempio):</span><span class="sxs-lookup"><span data-stu-id="ba8bc-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="ba8bc-173">L'esempio seguente è analogo all'esempio precedente, ad eccezione del fatto che:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="ba8bc-174">Per inviare i dati del modulo, viene usato JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="ba8bc-175">Nessuna convalida.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-175">There's no validation.</span></span>

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

<span data-ttu-id="ba8bc-176">Per eseguire il POST del form in JavaScript per i client che [non supportano l'API fetch](https://caniuse.com/#feat=fetch), usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="ba8bc-177">Usare un riempimento di recupero (ad esempio, [Window. fetch Refill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="ba8bc-178">Usare `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="ba8bc-179">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-179">For example:</span></span>

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

<span data-ttu-id="ba8bc-180">Per supportare il caricamento di file, i moduli HTML devono specificare un tipo di codifica ( `enctype` ) di `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="ba8bc-181">Affinché un `files` elemento input supporti il caricamento di più file, fornire l' `multiple` attributo nell' `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="ba8bc-182">È possibile accedere ai singoli file caricati nel server tramite l' [associazione di modelli](xref:mvc/models/model-binding) tramite <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="ba8bc-183">L'app di esempio illustra più caricamenti di file memorizzati nel buffer per scenari di archiviazione di database e fisici.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="ba8bc-184">Non **utilizzare la** `FileName` proprietà di <xref:Microsoft.AspNetCore.Http.IFormFile> diverso da per la visualizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="ba8bc-185">Durante la visualizzazione o la registrazione, HTML codifica il nome del file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="ba8bc-186">Un utente malintenzionato può fornire un nome file dannoso, inclusi percorsi completi o percorsi relativi.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="ba8bc-187">Le applicazioni devono:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-187">Applications should:</span></span>
>
> * <span data-ttu-id="ba8bc-188">Rimuovere il percorso dal nome file specificato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="ba8bc-189">Salvare il nome file con codifica HTML, percorso-rimosso per l'interfaccia utente o la registrazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="ba8bc-190">Genera un nuovo nome file casuale per l'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="ba8bc-191">Il codice seguente rimuove il percorso dal nome del file:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="ba8bc-192">Gli esempi forniti finora non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="ba8bc-193">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="ba8bc-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ba8bc-194">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="ba8bc-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ba8bc-195">Convalida</span><span class="sxs-lookup"><span data-stu-id="ba8bc-195">Validation</span></span>](#validation)

<span data-ttu-id="ba8bc-196">Quando si caricano file usando l'associazione di modelli e <xref:Microsoft.AspNetCore.Http.IFormFile> , il metodo di azione può accettare:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="ba8bc-197">Singolo oggetto <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="ba8bc-198">Qualsiasi raccolta seguente che rappresenta diversi file:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="ba8bc-199">[Elenco](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="ba8bc-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="ba8bc-200">L'associazione corrisponde ai file di form in base al nome.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-200">Binding matches form files by name.</span></span> <span data-ttu-id="ba8bc-201">Ad esempio, il `name` valore HTML in `<input type="file" name="formFile">` deve corrispondere al parametro/proprietà di C# associato ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="ba8bc-202">Per ulteriori informazioni, vedere la sezione [corrispondenza del nome dell'attributo del nome del parametro del metodo post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="ba8bc-203">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-203">The following example:</span></span>

* <span data-ttu-id="ba8bc-204">Esegue il ciclo di uno o più file caricati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="ba8bc-205">USA [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per restituire un percorso completo per un file, incluso il nome del file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="ba8bc-206">Salva i file nella file system locale usando un nome file generato dall'app.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="ba8bc-207">Restituisce il numero totale e le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="ba8bc-208">Utilizzare `Path.GetRandomFileName` per generare un nome file senza percorso.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="ba8bc-209">Nell'esempio seguente il percorso viene ottenuto dalla configurazione:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="ba8bc-210">Il percorso passato a <xref:System.IO.FileStream> *deve* includere il nome del file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="ba8bc-211">Se il nome file non viene specificato, <xref:System.UnauthorizedAccessException> viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="ba8bc-212">I file caricati con la <xref:Microsoft.AspNetCore.Http.IFormFile> tecnica vengono memorizzati nel buffer in memoria o su disco sul server prima dell'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="ba8bc-213">All'interno del metodo di azione, il <xref:Microsoft.AspNetCore.Http.IFormFile> contenuto è accessibile come <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="ba8bc-214">Oltre al file system locale, i file possono essere salvati in una condivisione di rete o in un servizio di archiviazione file, ad esempio [archiviazione BLOB di Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="ba8bc-215">Per un altro esempio che esegue il ciclo su più file per il caricamento e usa nomi di file sicuri, vedere *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="ba8bc-216">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) genera un'eccezione <xref:System.IO.IOException> se vengono creati più di 65.535 file senza eliminare i file temporanei precedenti.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="ba8bc-217">Il limite di 65.535 file è un limite per server.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="ba8bc-218">Per ulteriori informazioni su questo limite per il sistema operativo Windows, vedere la sezione Osservazioni negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="ba8bc-219">GetTempFileNameA (funzione)</span><span class="sxs-lookup"><span data-stu-id="ba8bc-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="ba8bc-220">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer in un database</span><span class="sxs-lookup"><span data-stu-id="ba8bc-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="ba8bc-221">Per archiviare i dati dei file binari in un database utilizzando [Entity Framework](/ef/core/index), definire una <xref:System.Byte> proprietà di matrice nell'entità:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="ba8bc-222">Specificare una proprietà del modello di pagina per la classe che include <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="ba8bc-223"><xref:Microsoft.AspNetCore.Http.IFormFile> può essere usato direttamente come parametro del metodo di azione o come proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="ba8bc-224">Nell'esempio precedente viene utilizzata una proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="ba8bc-225">Il `FileUpload` viene utilizzato nel Razor form pagine:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="ba8bc-226">Quando il modulo viene inviato al server, copiarlo in <xref:Microsoft.AspNetCore.Http.IFormFile> un flusso e salvarlo come una matrice di byte nel database.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="ba8bc-227">Nell'esempio seguente, `_dbContext` archivia il contesto di database dell'app:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="ba8bc-228">L'esempio precedente è simile a uno scenario illustrato nell'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="ba8bc-229">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba8bc-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="ba8bc-230">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="ba8bc-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="ba8bc-231">Quando si archiviano dati binari all'interno di database relazionali, è necessario prestare attenzione, perché l'operazione può influire negativamente sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="ba8bc-232">Non fare affidamento o considerare attendibile la `FileName` proprietà di <xref:Microsoft.AspNetCore.Http.IFormFile> senza convalida.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="ba8bc-233">La `FileName` proprietà deve essere usata solo a scopo di visualizzazione e solo dopo la codifica HTML.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="ba8bc-234">Gli esempi forniti non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="ba8bc-235">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="ba8bc-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ba8bc-236">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="ba8bc-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ba8bc-237">Convalida</span><span class="sxs-lookup"><span data-stu-id="ba8bc-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="ba8bc-238">Caricare file di grandi dimensioni con flusso</span><span class="sxs-lookup"><span data-stu-id="ba8bc-238">Upload large files with streaming</span></span>

<span data-ttu-id="ba8bc-239">Nell'esempio seguente viene illustrato come utilizzare JavaScript per trasmettere un file a un'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="ba8bc-240">Il token antifalsificazione del file viene generato usando un attributo di filtro personalizzato e passato alle intestazioni HTTP del client anziché nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="ba8bc-241">Poiché il metodo di azione elabora direttamente i dati caricati, l'associazione del modello di modulo viene disabilitata da un altro filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="ba8bc-242">All'interno dell'azione, il contenuto del form viene letto tramite un `MultipartReader`, che legge ogni singola `MultipartSection`, elaborando il file o archiviandone il contenuto, come appropriato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="ba8bc-243">Una volta lette le sezioni multiparte, l'azione esegue una propria associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="ba8bc-244">La risposta della pagina iniziale carica il form e salva un token antifalsificazione in un cookie (tramite l' `GenerateAntiforgeryTokenCookieAttribute` attributo).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="ba8bc-245">L'attributo usa il [supporto antifalsificazione](xref:security/anti-request-forgery) predefinito di ASP.NET Core per impostare un cookie con un token di richiesta:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="ba8bc-246">`DisableFormValueModelBindingAttribute`Viene usato per disabilitare l'associazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="ba8bc-247">Nell'app di esempio `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` vengono applicati come filtri ai modelli di applicazione della pagina di `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` all' `Startup.ConfigureServices` uso delle [ Razor convenzioni delle pagine](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="ba8bc-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="ba8bc-248">Poiché l'associazione di modelli non legge il modulo, i parametri associati al modulo non vengono associati (la query, la route e l'intestazione continuano a funzionare).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="ba8bc-249">Il metodo di azione funziona direttamente con la `Request` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="ba8bc-250">Per leggere ogni sezione, viene usato un `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="ba8bc-251">I dati chiave/valore vengono archiviati in un oggetto `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="ba8bc-252">Una volta lette le sezioni multipart, il contenuto di `KeyValueAccumulator` viene utilizzato per associare i dati del modulo a un tipo di modello.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="ba8bc-253">Metodo completo `StreamingController.UploadDatabase` per lo streaming in un database con EF Core:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="ba8bc-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="ba8bc-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="ba8bc-255">Il `StreamingController.UploadPhysical` metodo completo per lo streaming in una posizione fisica:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="ba8bc-256">Nell'app di esempio, i controlli di convalida vengono gestiti da `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="ba8bc-257">Convalida</span><span class="sxs-lookup"><span data-stu-id="ba8bc-257">Validation</span></span>

<span data-ttu-id="ba8bc-258">La classe dell'app di esempio `FileHelpers` illustra alcuni controlli per i caricamenti di file memorizzati nel buffer <xref:Microsoft.AspNetCore.Http.IFormFile> e in flusso.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="ba8bc-259">Per l'elaborazione dei <xref:Microsoft.AspNetCore.Http.IFormFile> caricamenti di file memorizzati nel buffer nell'app di esempio, vedere il `ProcessFormFile` metodo nel file *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="ba8bc-260">Per l'elaborazione di file trasmessi, vedere il `ProcessStreamedFile` Metodo nello stesso file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="ba8bc-261">I metodi di elaborazione della convalida illustrati nell'app di esempio non analizzano il contenuto dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="ba8bc-262">Nella maggior parte degli scenari di produzione, nel file viene usata un'API scanner di virus/malware prima di rendere disponibile il file per gli utenti o altri sistemi.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="ba8bc-263">Sebbene l'esempio di argomento fornisca un esempio funzionante di tecniche di convalida, non implementare la `FileHelpers` classe in un'app di produzione a meno che non si:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="ba8bc-264">Comprendere completamente l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="ba8bc-265">Modificare l'implementazione in modo appropriato per l'ambiente e le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="ba8bc-266">**Non implementare mai indiscriminatamente il codice di sicurezza in un'app senza soddisfare questi requisiti.**</span><span class="sxs-lookup"><span data-stu-id="ba8bc-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="ba8bc-267">Convalida contenuto</span><span class="sxs-lookup"><span data-stu-id="ba8bc-267">Content validation</span></span>

<span data-ttu-id="ba8bc-268">**Usare un'API di analisi di virus/malware di terze parti nel contenuto caricato.**</span><span class="sxs-lookup"><span data-stu-id="ba8bc-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="ba8bc-269">L'analisi dei file richiede le risorse del server in scenari con volumi elevati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="ba8bc-270">Se le prestazioni di elaborazione delle richieste sono diminuite a causa dell'analisi dei file, provare a eseguire l'offload del lavoro di analisi in un [servizio in background](xref:fundamentals/host/hosted-services), possibilmente in un servizio in esecuzione in un server diverso dal server dell'app.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="ba8bc-271">In genere, i file caricati vengono mantenuti in un'area in quarantena finché non vengono controllati dal programma antivirus in background.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="ba8bc-272">Quando un file passa, il file viene spostato nel percorso di archiviazione file normale.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="ba8bc-273">Questi passaggi vengono in genere eseguiti insieme a un record di database che indica lo stato di analisi di un file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="ba8bc-274">Usando un approccio di questo tipo, l'app e il server app rimangono incentrati sulla risposta alle richieste.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="ba8bc-275">Convalida dell'estensione di file</span><span class="sxs-lookup"><span data-stu-id="ba8bc-275">File extension validation</span></span>

<span data-ttu-id="ba8bc-276">L'estensione del file caricato deve essere verificata rispetto a un elenco di estensioni consentite.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="ba8bc-277">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="ba8bc-278">Convalida della firma del file</span><span class="sxs-lookup"><span data-stu-id="ba8bc-278">File signature validation</span></span>

<span data-ttu-id="ba8bc-279">La firma di un file è determinata dai primi byte all'inizio di un file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="ba8bc-280">Questi byte possono essere usati per indicare se l'estensione corrisponde al contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="ba8bc-281">L'app di esempio controlla le firme dei file per alcuni tipi di file comuni.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="ba8bc-282">Nell'esempio seguente, la firma del file per un'immagine JPEG viene verificata rispetto al file:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="ba8bc-283">Per ottenere ulteriori firme di file, vedere il [database delle firme file](https://www.filesignatures.net/) e le specifiche del file ufficiale.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="ba8bc-284">Sicurezza del nome file</span><span class="sxs-lookup"><span data-stu-id="ba8bc-284">File name security</span></span>

<span data-ttu-id="ba8bc-285">Non usare mai un nome file fornito dal client per salvare un file nell'archiviazione fisica.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="ba8bc-286">Creare un nome file sicuro per il file usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per creare un percorso completo (incluso il nome file) per l'archiviazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="ba8bc-287">Razor codifica HTML automaticamente i valori delle proprietà per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="ba8bc-288">Il codice seguente è sicuro da usare:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="ba8bc-289">Al di fuori di Razor , sempre il <xref:System.Net.WebUtility.HtmlEncode*> contenuto del nome file da una richiesta dell'utente.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="ba8bc-290">Molte implementazioni devono includere un controllo per verificare che il file esista. in caso contrario, il file viene sovrascritto da un file con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="ba8bc-291">Fornire logica aggiuntiva per soddisfare le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="ba8bc-292">Convalida dimensioni</span><span class="sxs-lookup"><span data-stu-id="ba8bc-292">Size validation</span></span>

<span data-ttu-id="ba8bc-293">Limitare le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="ba8bc-294">Nell'app di esempio, le dimensioni del file sono limitate a 2 MB (indicate in byte).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="ba8bc-295">Il limite viene fornito tramite la [configurazione](xref:fundamentals/configuration/index) dal *appsettings.jsnel* file:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="ba8bc-296">`FileSizeLimit`Viene inserito nelle `PageModel` classi:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="ba8bc-297">Quando le dimensioni di un file superano il limite, il file viene rifiutato:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="ba8bc-298">Corrisponde al valore dell'attributo Name al nome del parametro del metodo POST</span><span class="sxs-lookup"><span data-stu-id="ba8bc-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="ba8bc-299">In non Razor form che inviano dati del modulo o usano direttamente il nome di JavaScript `FormData` , il nome specificato nell'elemento del form o `FormData` deve corrispondere al nome del parametro nell'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="ba8bc-300">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-300">In the following example:</span></span>

* <span data-ttu-id="ba8bc-301">Quando si usa un `<input>` elemento, l' `name` attributo viene impostato sul valore `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="ba8bc-302">Quando `FormData` si usa in JavaScript, il nome viene impostato sul valore `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="ba8bc-303">Usare un nome corrispondente per il parametro del metodo C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="ba8bc-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="ba8bc-304">Per un Razor metodo gestore pagina pagine denominato `Upload` :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="ba8bc-305">Per un metodo di azione POST controller MVC:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="ba8bc-306">Configurazione del server e dell'app</span><span class="sxs-lookup"><span data-stu-id="ba8bc-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="ba8bc-307">Limite di lunghezza del corpo multipart</span><span class="sxs-lookup"><span data-stu-id="ba8bc-307">Multipart body length limit</span></span>

<span data-ttu-id="ba8bc-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> imposta il limite per la lunghezza di ogni corpo multipart.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="ba8bc-309">Le sezioni del modulo che superano questo limite generano un'espressione <xref:System.IO.InvalidDataException> quando vengono analizzate.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="ba8bc-310">Il valore predefinito è 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="ba8bc-311">Personalizzare il limite usando l' <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> impostazione in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ba8bc-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> viene usato per impostare <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="ba8bc-313">In un' Razor app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
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

<span data-ttu-id="ba8bc-314">In un' Razor app pagine o in un'app MVC, applicare il filtro al modello di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="ba8bc-315">Dimensioni massime del corpo della richiesta di gheppio</span><span class="sxs-lookup"><span data-stu-id="ba8bc-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="ba8bc-316">Per le app ospitate da gheppio, le dimensioni massime predefinite del corpo della richiesta sono pari a 30 milioni byte, ovvero circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ba8bc-317">Personalizzare il limite usando l'opzione del server [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) gheppio:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="ba8bc-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> viene usato per impostare [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="ba8bc-319">In un' Razor app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
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

<span data-ttu-id="ba8bc-320">In un' Razor app pagine o in un'app MVC, applicare il filtro alla classe del gestore di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="ba8bc-321">Il `RequestSizeLimitAttribute` può essere applicato anche usando la [`@attribute`](xref:mvc/views/razor#attribute) Razor direttiva:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="ba8bc-322">Altri limiti del gheppio</span><span class="sxs-lookup"><span data-stu-id="ba8bc-322">Other Kestrel limits</span></span>

<span data-ttu-id="ba8bc-323">Per le app ospitate da gheppio possono essere applicati altri limiti di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="ba8bc-324">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="ba8bc-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="ba8bc-325">Frequenza dei dati di richiesta e risposta</span><span class="sxs-lookup"><span data-stu-id="ba8bc-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="ba8bc-326">Limite lunghezza contenuto IIS</span><span class="sxs-lookup"><span data-stu-id="ba8bc-326">IIS content length limit</span></span>

<span data-ttu-id="ba8bc-327">Il limite di richieste predefinito ( `maxAllowedContentLength` ) è 30 milioni byte, che corrisponde a circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="ba8bc-328">Personalizzare il limite nel file di *web.config* :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-328">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="ba8bc-329">Questa impostazione si applica solo a IIS.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-329">This setting only applies to IIS.</span></span> <span data-ttu-id="ba8bc-330">Il comportamento non si verifica per impostazione predefinita con l'hosting in Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="ba8bc-331">Per altre informazioni, vedere [limiti \<requestLimits> della richiesta ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="ba8bc-332">Le limitazioni del modulo ASP.NET Core o della presenza del modulo filtro richieste IIS possono limitare il caricamento a 2 o 4 GB.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="ba8bc-333">Per altre informazioni, vedere [non è possibile caricare file di dimensioni superiori a 2 GB (DotNet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ba8bc-334">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="ba8bc-334">Troubleshoot</span></span>

<span data-ttu-id="ba8bc-335">Di seguito sono trattati alcuni problemi comuni riscontrati durante il caricamento di file, con le soluzioni possibili corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="ba8bc-336">Errore non trovato quando viene distribuito in un server IIS</span><span class="sxs-lookup"><span data-stu-id="ba8bc-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="ba8bc-337">L'errore seguente indica che il file caricato supera la lunghezza del contenuto configurata del server:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="ba8bc-338">Per ulteriori informazioni sull'aumento del limite, vedere la sezione [limite di lunghezza del contenuto IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="ba8bc-339">Errore di connessione</span><span class="sxs-lookup"><span data-stu-id="ba8bc-339">Connection failure</span></span>

<span data-ttu-id="ba8bc-340">Un errore di connessione e una connessione al server di reimpostazione probabilmente indicano che il file caricato supera le dimensioni massime del corpo della richiesta del gheppio.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="ba8bc-341">Per ulteriori informazioni, vedere la sezione relativa alle [dimensioni massime del corpo della richiesta di Gheppio](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="ba8bc-342">I limiti di connessione del client gheppio possono richiedere anche modifiche.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="ba8bc-343">Eccezione per riferimento Null con IFormFile</span><span class="sxs-lookup"><span data-stu-id="ba8bc-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="ba8bc-344">Se il controller accetta file caricati utilizzando <xref:Microsoft.AspNetCore.Http.IFormFile> , ma il valore è `null` , verificare che il form HTML specifichi un `enctype` valore di `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="ba8bc-345">Se questo attributo non è impostato sull' `<form>` elemento, il caricamento del file non viene eseguito e gli <xref:Microsoft.AspNetCore.Http.IFormFile> argomenti associati sono `null` .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="ba8bc-346">Verificare inoltre che la [denominazione di caricamento nei dati del modulo corrisponda alla denominazione dell'app](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="ba8bc-347">Il flusso è troppo lungo</span><span class="sxs-lookup"><span data-stu-id="ba8bc-347">Stream was too long</span></span>

<span data-ttu-id="ba8bc-348">Gli esempi in questo argomento si basano su <xref:System.IO.MemoryStream> per conservare il contenuto del file caricato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="ba8bc-349">Il limite delle dimensioni di un oggetto `MemoryStream` è `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="ba8bc-350">Se lo scenario di caricamento dei file dell'app richiede un contenuto del file di dimensioni superiori a 50 MB, usare un approccio alternativo che non si basa su un singolo `MemoryStream` per contenere il contenuto di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ba8bc-351">ASP.NET Core supporta il caricamento di uno o più file mediante l'associazione di modelli memorizzati nel buffer per i file più piccoli e il flusso non memorizzato nel buffer per file di dimensioni maggiori.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="ba8bc-352">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ba8bc-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="ba8bc-353">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="ba8bc-353">Security considerations</span></span>

<span data-ttu-id="ba8bc-354">Prestare attenzione quando si fornisce agli utenti la possibilità di caricare file in un server.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="ba8bc-355">Gli utenti malintenzionati possono tentare di:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="ba8bc-356">Eseguire attacchi [di tipo Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="ba8bc-357">Caricare virus o malware.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="ba8bc-358">Compromettere le reti e i server in altri modi.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="ba8bc-359">I passaggi di sicurezza che riducono la probabilità di un attacco riuscito sono:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="ba8bc-360">Caricare i file in un'area di caricamento file dedicata, preferibilmente in un'unità non di sistema.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="ba8bc-361">Un percorso dedicato semplifica l'imposizione delle restrizioni di sicurezza sui file caricati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="ba8bc-362">Disabilitare le autorizzazioni di esecuzione per il percorso di caricamento del file.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba8bc-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="ba8bc-363">Non **rende permanente i** file caricati nella stessa struttura di directory dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba8bc-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="ba8bc-364">Usare un nome file sicuro determinato dall'app.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="ba8bc-365">Non usare un nome file fornito dall'utente o il nome file non attendibile del file caricato. &dagger; HTML codifica il nome file non attendibile quando viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="ba8bc-366">Ad esempio, registrare il nome del file o visualizzarlo nell'interfaccia utente (l' Razor output viene codificato automaticamente in HTML).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="ba8bc-367">Consenti solo le estensioni di file approvate per la specifica di progettazione dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba8bc-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="ba8bc-368">Verificare che i controlli lato client vengano eseguiti sul server. &dagger; I controlli lato client sono facili da aggirare.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="ba8bc-369">Controllare le dimensioni di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="ba8bc-370">Impostare un limite di dimensioni massime per impedire caricamenti di grandi dimensioni.&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba8bc-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="ba8bc-371">Quando i file non devono essere sovrascritti da un file caricato con lo stesso nome, controllare il nome del file sul database o sull'archiviazione fisica prima di caricare il file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="ba8bc-372">**Eseguire uno scanner virus/malware sul contenuto caricato prima che il file venga archiviato.**</span><span class="sxs-lookup"><span data-stu-id="ba8bc-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="ba8bc-373">&dagger;L'app di esempio illustra un approccio che soddisfa i criteri.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="ba8bc-374">Il caricamento di codice dannoso in un sistema è spesso il primo passaggio per l'esecuzione di codice in grado di:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="ba8bc-375">Ottenere il controllo completo di un sistema.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="ba8bc-376">Sovraccaricare un sistema con il risultato che si verifica un arresto anomalo del sistema.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="ba8bc-377">Compromettere i dati di sistemi o utenti.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="ba8bc-378">Applicare i graffiti a un'interfaccia utente pubblica.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="ba8bc-379">Per informazioni sulla riduzione della superficie di attacco quando si accettano file dagli utenti, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="ba8bc-380">Caricamento di file senza restrizioni</span><span class="sxs-lookup"><span data-stu-id="ba8bc-380">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="ba8bc-381">Protezione di Azure: Verificare i controlli appropriati quando si accettano file dagli utenti</span><span class="sxs-lookup"><span data-stu-id="ba8bc-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="ba8bc-382">Per ulteriori informazioni sull'implementazione di misure di sicurezza, inclusi esempi dall'app di esempio, vedere la sezione [convalida](#validation) .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="ba8bc-383">Scenari di archiviazione</span><span class="sxs-lookup"><span data-stu-id="ba8bc-383">Storage scenarios</span></span>

<span data-ttu-id="ba8bc-384">Le opzioni di archiviazione comuni per i file includono:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-384">Common storage options for files include:</span></span>

* <span data-ttu-id="ba8bc-385">Database</span><span class="sxs-lookup"><span data-stu-id="ba8bc-385">Database</span></span>

  * <span data-ttu-id="ba8bc-386">Per i caricamenti di file di piccole dimensioni, un database è spesso più veloce rispetto alle opzioni di archiviazione fisica (file system o condivisione di rete).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="ba8bc-387">Un database è spesso più pratico delle opzioni di archiviazione fisica perché il recupero di un record di database per i dati utente può fornire simultaneamente il contenuto del file, ad esempio un'immagine avatar.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="ba8bc-388">Un database è potenzialmente meno costoso rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="ba8bc-389">Archiviazione fisica (file system o condivisione di rete)</span><span class="sxs-lookup"><span data-stu-id="ba8bc-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="ba8bc-390">Per i caricamenti di file di grandi dimensioni:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-390">For large file uploads:</span></span>
    * <span data-ttu-id="ba8bc-391">I limiti del database possono limitare le dimensioni del caricamento.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="ba8bc-392">L'archiviazione fisica è spesso meno economica rispetto all'archiviazione in un database.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="ba8bc-393">L'archiviazione fisica è potenzialmente meno costosa rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="ba8bc-394">Il processo dell'app deve avere le autorizzazioni di lettura e scrittura per il percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="ba8bc-395">**Non concedere mai l'autorizzazione Execute.**</span><span class="sxs-lookup"><span data-stu-id="ba8bc-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="ba8bc-396">Servizio di archiviazione dei dati (ad esempio, [archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="ba8bc-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="ba8bc-397">I servizi in genere offrono una maggiore scalabilità e resilienza rispetto a soluzioni locali che in genere sono soggette a singoli punti di errore.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="ba8bc-398">I servizi sono un costo potenzialmente inferiore in scenari di infrastruttura di archiviazione di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="ba8bc-399">Per altre informazioni, vedere [Guida introduttiva: usare .NET per creare un BLOB nell'archivio oggetti](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="ba8bc-400">Nell'argomento viene illustrato <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , ma <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> è possibile utilizzare per salvare un <xref:System.IO.FileStream> nell'archivio BLOB quando si utilizza un <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="ba8bc-401">Scenari di caricamento di file</span><span class="sxs-lookup"><span data-stu-id="ba8bc-401">File upload scenarios</span></span>

<span data-ttu-id="ba8bc-402">Due approcci generali per il caricamento di file sono il buffering e il flusso.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="ba8bc-403">**responseBuffering**</span><span class="sxs-lookup"><span data-stu-id="ba8bc-403">**Buffering**</span></span>

<span data-ttu-id="ba8bc-404">L'intero file viene letto in un oggetto <xref:Microsoft.AspNetCore.Http.IFormFile> , ovvero una rappresentazione C# del file usato per elaborare o salvare il file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="ba8bc-405">Le risorse (disco, memoria) utilizzate dai caricamenti di file dipendono dal numero e dalle dimensioni dei caricamenti di file simultanei.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="ba8bc-406">Se un'app tenta di memorizzare nel buffer un numero eccessivo di caricamenti, il sito si arresta in modo anomalo quando esaurisce la memoria o lo spazio su disco.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="ba8bc-407">Se le dimensioni o la frequenza dei caricamenti di file esauriscono le risorse dell'app, usare il flusso.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="ba8bc-408">Ogni singolo file memorizzato nel buffer che supera 64 KB viene spostato dalla memoria in un file temporaneo su disco.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="ba8bc-409">Il buffering di file di piccole dimensioni viene trattato nelle sezioni seguenti di questo argomento:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="ba8bc-410">Archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="ba8bc-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="ba8bc-411">Database</span><span class="sxs-lookup"><span data-stu-id="ba8bc-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="ba8bc-412">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="ba8bc-412">**Streaming**</span></span>

<span data-ttu-id="ba8bc-413">Il file viene ricevuto da una richiesta multipart ed elaborato o salvato direttamente dall'app.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="ba8bc-414">Il flusso non migliora significativamente le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="ba8bc-415">Il flusso riduce le richieste di memoria o di spazio su disco durante il caricamento dei file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="ba8bc-416">Il flusso di file di grandi dimensioni è trattato nella sezione [caricare file di grandi dimensioni con flusso](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="ba8bc-417">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer all'archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="ba8bc-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="ba8bc-418">Per caricare file di piccole dimensioni, usare un modulo multipart o creare una richiesta POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="ba8bc-419">L'esempio seguente illustra l'uso di un Razor form di pagine per caricare un singolo file (*pages/BufferedSingleFileUploadPhysical. cshtml* nell'app di esempio):</span><span class="sxs-lookup"><span data-stu-id="ba8bc-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="ba8bc-420">L'esempio seguente è analogo all'esempio precedente, ad eccezione del fatto che:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="ba8bc-421">Per inviare i dati del modulo, viene usato JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="ba8bc-422">Nessuna convalida.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-422">There's no validation.</span></span>

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

<span data-ttu-id="ba8bc-423">Per eseguire il POST del form in JavaScript per i client che [non supportano l'API fetch](https://caniuse.com/#feat=fetch), usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="ba8bc-424">Usare un riempimento di recupero (ad esempio, [Window. fetch Refill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="ba8bc-425">Usare `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="ba8bc-426">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-426">For example:</span></span>

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

<span data-ttu-id="ba8bc-427">Per supportare il caricamento di file, i moduli HTML devono specificare un tipo di codifica ( `enctype` ) di `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="ba8bc-428">Affinché un `files` elemento input supporti il caricamento di più file, fornire l' `multiple` attributo nell' `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="ba8bc-429">È possibile accedere ai singoli file caricati nel server tramite l' [associazione di modelli](xref:mvc/models/model-binding) tramite <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="ba8bc-430">L'app di esempio illustra più caricamenti di file memorizzati nel buffer per scenari di archiviazione di database e fisici.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="ba8bc-431">Non **utilizzare la** `FileName` proprietà di <xref:Microsoft.AspNetCore.Http.IFormFile> diverso da per la visualizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="ba8bc-432">Durante la visualizzazione o la registrazione, HTML codifica il nome del file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="ba8bc-433">Un utente malintenzionato può fornire un nome file dannoso, inclusi percorsi completi o percorsi relativi.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="ba8bc-434">Le applicazioni devono:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-434">Applications should:</span></span>
>
> * <span data-ttu-id="ba8bc-435">Rimuovere il percorso dal nome file specificato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="ba8bc-436">Salvare il nome file con codifica HTML, percorso-rimosso per l'interfaccia utente o la registrazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="ba8bc-437">Genera un nuovo nome file casuale per l'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="ba8bc-438">Il codice seguente rimuove il percorso dal nome del file:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="ba8bc-439">Gli esempi forniti finora non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="ba8bc-440">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="ba8bc-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ba8bc-441">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="ba8bc-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ba8bc-442">Convalida</span><span class="sxs-lookup"><span data-stu-id="ba8bc-442">Validation</span></span>](#validation)

<span data-ttu-id="ba8bc-443">Quando si caricano file usando l'associazione di modelli e <xref:Microsoft.AspNetCore.Http.IFormFile> , il metodo di azione può accettare:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="ba8bc-444">Singolo oggetto <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="ba8bc-445">Qualsiasi raccolta seguente che rappresenta diversi file:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="ba8bc-446">[Elenco](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="ba8bc-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="ba8bc-447">L'associazione corrisponde ai file di form in base al nome.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-447">Binding matches form files by name.</span></span> <span data-ttu-id="ba8bc-448">Ad esempio, il `name` valore HTML in `<input type="file" name="formFile">` deve corrispondere al parametro/proprietà di C# associato ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="ba8bc-449">Per ulteriori informazioni, vedere la sezione [corrispondenza del nome dell'attributo del nome del parametro del metodo post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="ba8bc-450">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-450">The following example:</span></span>

* <span data-ttu-id="ba8bc-451">Esegue il ciclo di uno o più file caricati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="ba8bc-452">USA [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per restituire un percorso completo per un file, incluso il nome del file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="ba8bc-453">Salva i file nella file system locale usando un nome file generato dall'app.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="ba8bc-454">Restituisce il numero totale e le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-454">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="ba8bc-455">Utilizzare `Path.GetRandomFileName` per generare un nome file senza percorso.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="ba8bc-456">Nell'esempio seguente il percorso viene ottenuto dalla configurazione:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-456">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="ba8bc-457">Il percorso passato a <xref:System.IO.FileStream> *deve* includere il nome del file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="ba8bc-458">Se il nome file non viene specificato, <xref:System.UnauthorizedAccessException> viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="ba8bc-459">I file caricati con la <xref:Microsoft.AspNetCore.Http.IFormFile> tecnica vengono memorizzati nel buffer in memoria o su disco sul server prima dell'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="ba8bc-460">All'interno del metodo di azione, il <xref:Microsoft.AspNetCore.Http.IFormFile> contenuto è accessibile come <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="ba8bc-461">Oltre al file system locale, i file possono essere salvati in una condivisione di rete o in un servizio di archiviazione file, ad esempio [archiviazione BLOB di Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="ba8bc-462">Per un altro esempio che esegue il ciclo su più file per il caricamento e usa nomi di file sicuri, vedere *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="ba8bc-463">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) genera un'eccezione <xref:System.IO.IOException> se vengono creati più di 65.535 file senza eliminare i file temporanei precedenti.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="ba8bc-464">Il limite di 65.535 file è un limite per server.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="ba8bc-465">Per ulteriori informazioni su questo limite per il sistema operativo Windows, vedere la sezione Osservazioni negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="ba8bc-466">GetTempFileNameA (funzione)</span><span class="sxs-lookup"><span data-stu-id="ba8bc-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="ba8bc-467">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer in un database</span><span class="sxs-lookup"><span data-stu-id="ba8bc-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="ba8bc-468">Per archiviare i dati dei file binari in un database utilizzando [Entity Framework](/ef/core/index), definire una <xref:System.Byte> proprietà di matrice nell'entità:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="ba8bc-469">Specificare una proprietà del modello di pagina per la classe che include <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="ba8bc-470"><xref:Microsoft.AspNetCore.Http.IFormFile> può essere usato direttamente come parametro del metodo di azione o come proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="ba8bc-471">Nell'esempio precedente viene utilizzata una proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="ba8bc-472">Il `FileUpload` viene utilizzato nel Razor form pagine:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-472">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="ba8bc-473">Quando il modulo viene inviato al server, copiarlo in <xref:Microsoft.AspNetCore.Http.IFormFile> un flusso e salvarlo come una matrice di byte nel database.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="ba8bc-474">Nell'esempio seguente, `_dbContext` archivia il contesto di database dell'app:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-474">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="ba8bc-475">L'esempio precedente è simile a uno scenario illustrato nell'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="ba8bc-476">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba8bc-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="ba8bc-477">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="ba8bc-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="ba8bc-478">Quando si archiviano dati binari all'interno di database relazionali, è necessario prestare attenzione, perché l'operazione può influire negativamente sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="ba8bc-479">Non fare affidamento o considerare attendibile la `FileName` proprietà di <xref:Microsoft.AspNetCore.Http.IFormFile> senza convalida.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="ba8bc-480">La `FileName` proprietà deve essere usata solo a scopo di visualizzazione e solo dopo la codifica HTML.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="ba8bc-481">Gli esempi forniti non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="ba8bc-482">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="ba8bc-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="ba8bc-483">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="ba8bc-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="ba8bc-484">Convalida</span><span class="sxs-lookup"><span data-stu-id="ba8bc-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="ba8bc-485">Caricare file di grandi dimensioni con flusso</span><span class="sxs-lookup"><span data-stu-id="ba8bc-485">Upload large files with streaming</span></span>

<span data-ttu-id="ba8bc-486">Nell'esempio seguente viene illustrato come utilizzare JavaScript per trasmettere un file a un'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="ba8bc-487">Il token antifalsificazione del file viene generato usando un attributo di filtro personalizzato e passato alle intestazioni HTTP del client anziché nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="ba8bc-488">Poiché il metodo di azione elabora direttamente i dati caricati, l'associazione del modello di modulo viene disabilitata da un altro filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="ba8bc-489">All'interno dell'azione, il contenuto del form viene letto tramite un `MultipartReader`, che legge ogni singola `MultipartSection`, elaborando il file o archiviandone il contenuto, come appropriato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="ba8bc-490">Una volta lette le sezioni multiparte, l'azione esegue una propria associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="ba8bc-491">La risposta della pagina iniziale carica il form e salva un token antifalsificazione in un cookie (tramite l' `GenerateAntiforgeryTokenCookieAttribute` attributo).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="ba8bc-492">L'attributo usa il [supporto antifalsificazione](xref:security/anti-request-forgery) predefinito di ASP.NET Core per impostare un cookie con un token di richiesta:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="ba8bc-493">`DisableFormValueModelBindingAttribute`Viene usato per disabilitare l'associazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="ba8bc-494">Nell'app di esempio `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` vengono applicati come filtri ai modelli di applicazione della pagina di `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` all' `Startup.ConfigureServices` uso delle [ Razor convenzioni delle pagine](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="ba8bc-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="ba8bc-495">Poiché l'associazione di modelli non legge il modulo, i parametri associati al modulo non vengono associati (la query, la route e l'intestazione continuano a funzionare).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="ba8bc-496">Il metodo di azione funziona direttamente con la `Request` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="ba8bc-497">Per leggere ogni sezione, viene usato un `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="ba8bc-498">I dati chiave/valore vengono archiviati in un oggetto `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="ba8bc-499">Una volta lette le sezioni multipart, il contenuto di `KeyValueAccumulator` viene utilizzato per associare i dati del modulo a un tipo di modello.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="ba8bc-500">Metodo completo `StreamingController.UploadDatabase` per lo streaming in un database con EF Core:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="ba8bc-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="ba8bc-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="ba8bc-502">Il `StreamingController.UploadPhysical` metodo completo per lo streaming in una posizione fisica:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="ba8bc-503">Nell'app di esempio, i controlli di convalida vengono gestiti da `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="ba8bc-504">Convalida</span><span class="sxs-lookup"><span data-stu-id="ba8bc-504">Validation</span></span>

<span data-ttu-id="ba8bc-505">La classe dell'app di esempio `FileHelpers` illustra alcuni controlli per i caricamenti di file memorizzati nel buffer <xref:Microsoft.AspNetCore.Http.IFormFile> e in flusso.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="ba8bc-506">Per l'elaborazione dei <xref:Microsoft.AspNetCore.Http.IFormFile> caricamenti di file memorizzati nel buffer nell'app di esempio, vedere il `ProcessFormFile` metodo nel file *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="ba8bc-507">Per l'elaborazione di file trasmessi, vedere il `ProcessStreamedFile` Metodo nello stesso file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="ba8bc-508">I metodi di elaborazione della convalida illustrati nell'app di esempio non analizzano il contenuto dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="ba8bc-509">Nella maggior parte degli scenari di produzione, nel file viene usata un'API scanner di virus/malware prima di rendere disponibile il file per gli utenti o altri sistemi.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="ba8bc-510">Sebbene l'esempio di argomento fornisca un esempio funzionante di tecniche di convalida, non implementare la `FileHelpers` classe in un'app di produzione a meno che non si:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="ba8bc-511">Comprendere completamente l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="ba8bc-512">Modificare l'implementazione in modo appropriato per l'ambiente e le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="ba8bc-513">**Non implementare mai indiscriminatamente il codice di sicurezza in un'app senza soddisfare questi requisiti.**</span><span class="sxs-lookup"><span data-stu-id="ba8bc-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="ba8bc-514">Convalida contenuto</span><span class="sxs-lookup"><span data-stu-id="ba8bc-514">Content validation</span></span>

<span data-ttu-id="ba8bc-515">**Usare un'API di analisi di virus/malware di terze parti nel contenuto caricato.**</span><span class="sxs-lookup"><span data-stu-id="ba8bc-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="ba8bc-516">L'analisi dei file richiede le risorse del server in scenari con volumi elevati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="ba8bc-517">Se le prestazioni di elaborazione delle richieste sono diminuite a causa dell'analisi dei file, provare a eseguire l'offload del lavoro di analisi in un [servizio in background](xref:fundamentals/host/hosted-services), possibilmente in un servizio in esecuzione in un server diverso dal server dell'app.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="ba8bc-518">In genere, i file caricati vengono mantenuti in un'area in quarantena finché non vengono controllati dal programma antivirus in background.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="ba8bc-519">Quando un file passa, il file viene spostato nel percorso di archiviazione file normale.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="ba8bc-520">Questi passaggi vengono in genere eseguiti insieme a un record di database che indica lo stato di analisi di un file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="ba8bc-521">Usando un approccio di questo tipo, l'app e il server app rimangono incentrati sulla risposta alle richieste.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="ba8bc-522">Convalida dell'estensione di file</span><span class="sxs-lookup"><span data-stu-id="ba8bc-522">File extension validation</span></span>

<span data-ttu-id="ba8bc-523">L'estensione del file caricato deve essere verificata rispetto a un elenco di estensioni consentite.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="ba8bc-524">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="ba8bc-525">Convalida della firma del file</span><span class="sxs-lookup"><span data-stu-id="ba8bc-525">File signature validation</span></span>

<span data-ttu-id="ba8bc-526">La firma di un file è determinata dai primi byte all'inizio di un file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="ba8bc-527">Questi byte possono essere usati per indicare se l'estensione corrisponde al contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="ba8bc-528">L'app di esempio controlla le firme dei file per alcuni tipi di file comuni.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="ba8bc-529">Nell'esempio seguente, la firma del file per un'immagine JPEG viene verificata rispetto al file:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="ba8bc-530">Per ottenere ulteriori firme di file, vedere il [database delle firme file](https://www.filesignatures.net/) e le specifiche del file ufficiale.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="ba8bc-531">Sicurezza del nome file</span><span class="sxs-lookup"><span data-stu-id="ba8bc-531">File name security</span></span>

<span data-ttu-id="ba8bc-532">Non usare mai un nome file fornito dal client per salvare un file nell'archiviazione fisica.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="ba8bc-533">Creare un nome file sicuro per il file usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per creare un percorso completo (incluso il nome file) per l'archiviazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="ba8bc-534">Razor codifica HTML automaticamente i valori delle proprietà per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-534">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="ba8bc-535">Il codice seguente è sicuro da usare:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="ba8bc-536">Al di fuori di Razor , sempre il <xref:System.Net.WebUtility.HtmlEncode*> contenuto del nome file da una richiesta dell'utente.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="ba8bc-537">Molte implementazioni devono includere un controllo per verificare che il file esista. in caso contrario, il file viene sovrascritto da un file con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="ba8bc-538">Fornire logica aggiuntiva per soddisfare le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="ba8bc-539">Convalida dimensioni</span><span class="sxs-lookup"><span data-stu-id="ba8bc-539">Size validation</span></span>

<span data-ttu-id="ba8bc-540">Limitare le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="ba8bc-541">Nell'app di esempio, le dimensioni del file sono limitate a 2 MB (indicate in byte).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="ba8bc-542">Il limite viene fornito tramite la [configurazione](xref:fundamentals/configuration/index) dal *appsettings.jsnel* file:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="ba8bc-543">`FileSizeLimit`Viene inserito nelle `PageModel` classi:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="ba8bc-544">Quando le dimensioni di un file superano il limite, il file viene rifiutato:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="ba8bc-545">Corrisponde al valore dell'attributo Name al nome del parametro del metodo POST</span><span class="sxs-lookup"><span data-stu-id="ba8bc-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="ba8bc-546">In non Razor form che inviano dati del modulo o usano direttamente il nome di JavaScript `FormData` , il nome specificato nell'elemento del form o `FormData` deve corrispondere al nome del parametro nell'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="ba8bc-547">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-547">In the following example:</span></span>

* <span data-ttu-id="ba8bc-548">Quando si usa un `<input>` elemento, l' `name` attributo viene impostato sul valore `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="ba8bc-549">Quando `FormData` si usa in JavaScript, il nome viene impostato sul valore `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="ba8bc-550">Usare un nome corrispondente per il parametro del metodo C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="ba8bc-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="ba8bc-551">Per un Razor metodo gestore pagina pagine denominato `Upload` :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="ba8bc-552">Per un metodo di azione POST controller MVC:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="ba8bc-553">Configurazione del server e dell'app</span><span class="sxs-lookup"><span data-stu-id="ba8bc-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="ba8bc-554">Limite di lunghezza del corpo multipart</span><span class="sxs-lookup"><span data-stu-id="ba8bc-554">Multipart body length limit</span></span>

<span data-ttu-id="ba8bc-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> imposta il limite per la lunghezza di ogni corpo multipart.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="ba8bc-556">Le sezioni del modulo che superano questo limite generano un'espressione <xref:System.IO.InvalidDataException> quando vengono analizzate.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="ba8bc-557">Il valore predefinito è 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="ba8bc-558">Personalizzare il limite usando l' <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> impostazione in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ba8bc-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> viene usato per impostare <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="ba8bc-560">In un' Razor app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ba8bc-561">In un' Razor app pagine o in un'app MVC, applicare il filtro al modello di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="ba8bc-562">Dimensioni massime del corpo della richiesta di gheppio</span><span class="sxs-lookup"><span data-stu-id="ba8bc-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="ba8bc-563">Per le app ospitate da gheppio, le dimensioni massime predefinite del corpo della richiesta sono pari a 30 milioni byte, ovvero circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ba8bc-564">Personalizzare il limite usando l'opzione del server [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) gheppio:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="ba8bc-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> viene usato per impostare [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="ba8bc-566">In un' Razor app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ba8bc-567">In un' Razor app pagine o in un'app MVC, applicare il filtro alla classe del gestore di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="ba8bc-568">Altri limiti del gheppio</span><span class="sxs-lookup"><span data-stu-id="ba8bc-568">Other Kestrel limits</span></span>

<span data-ttu-id="ba8bc-569">Per le app ospitate da gheppio possono essere applicati altri limiti di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="ba8bc-570">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="ba8bc-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="ba8bc-571">Frequenza dei dati di richiesta e risposta</span><span class="sxs-lookup"><span data-stu-id="ba8bc-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="ba8bc-572">Limite lunghezza contenuto IIS</span><span class="sxs-lookup"><span data-stu-id="ba8bc-572">IIS content length limit</span></span>

<span data-ttu-id="ba8bc-573">Il limite di richieste predefinito ( `maxAllowedContentLength` ) è 30 milioni byte, che corrisponde a circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="ba8bc-574">Personalizzare il limite nel file di *web.config* :</span><span class="sxs-lookup"><span data-stu-id="ba8bc-574">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="ba8bc-575">Questa impostazione si applica solo a IIS.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-575">This setting only applies to IIS.</span></span> <span data-ttu-id="ba8bc-576">Il comportamento non si verifica per impostazione predefinita con l'hosting in Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="ba8bc-577">Per altre informazioni, vedere [limiti \<requestLimits> della richiesta ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="ba8bc-578">Le limitazioni del modulo ASP.NET Core o della presenza del modulo filtro richieste IIS possono limitare il caricamento a 2 o 4 GB.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="ba8bc-579">Per altre informazioni, vedere [non è possibile caricare file di dimensioni superiori a 2 GB (DotNet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ba8bc-580">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="ba8bc-580">Troubleshoot</span></span>

<span data-ttu-id="ba8bc-581">Di seguito sono trattati alcuni problemi comuni riscontrati durante il caricamento di file, con le soluzioni possibili corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="ba8bc-582">Errore non trovato quando viene distribuito in un server IIS</span><span class="sxs-lookup"><span data-stu-id="ba8bc-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="ba8bc-583">L'errore seguente indica che il file caricato supera la lunghezza del contenuto configurata del server:</span><span class="sxs-lookup"><span data-stu-id="ba8bc-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="ba8bc-584">Per ulteriori informazioni sull'aumento del limite, vedere la sezione [limite di lunghezza del contenuto IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="ba8bc-585">Errore di connessione</span><span class="sxs-lookup"><span data-stu-id="ba8bc-585">Connection failure</span></span>

<span data-ttu-id="ba8bc-586">Un errore di connessione e una connessione al server di reimpostazione probabilmente indicano che il file caricato supera le dimensioni massime del corpo della richiesta del gheppio.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="ba8bc-587">Per ulteriori informazioni, vedere la sezione relativa alle [dimensioni massime del corpo della richiesta di Gheppio](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="ba8bc-588">I limiti di connessione del client gheppio possono richiedere anche modifiche.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="ba8bc-589">Eccezione per riferimento Null con IFormFile</span><span class="sxs-lookup"><span data-stu-id="ba8bc-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="ba8bc-590">Se il controller accetta file caricati utilizzando <xref:Microsoft.AspNetCore.Http.IFormFile> , ma il valore è `null` , verificare che il form HTML specifichi un `enctype` valore di `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="ba8bc-591">Se questo attributo non è impostato sull' `<form>` elemento, il caricamento del file non viene eseguito e gli <xref:Microsoft.AspNetCore.Http.IFormFile> argomenti associati sono `null` .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="ba8bc-592">Verificare inoltre che la [denominazione di caricamento nei dati del modulo corrisponda alla denominazione dell'app](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="ba8bc-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="ba8bc-593">Il flusso è troppo lungo</span><span class="sxs-lookup"><span data-stu-id="ba8bc-593">Stream was too long</span></span>

<span data-ttu-id="ba8bc-594">Gli esempi in questo argomento si basano su <xref:System.IO.MemoryStream> per conservare il contenuto del file caricato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="ba8bc-595">Il limite delle dimensioni di un oggetto `MemoryStream` è `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="ba8bc-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="ba8bc-596">Se lo scenario di caricamento dei file dell'app richiede un contenuto del file di dimensioni superiori a 50 MB, usare un approccio alternativo che non si basa su un singolo `MemoryStream` per contenere il contenuto di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="ba8bc-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="ba8bc-597">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="ba8bc-597">Additional resources</span></span>

* [<span data-ttu-id="ba8bc-598">Svuotamento della richiesta di connessione HTTP</span><span class="sxs-lookup"><span data-stu-id="ba8bc-598">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="ba8bc-599">Caricamento di file senza restrizioni</span><span class="sxs-lookup"><span data-stu-id="ba8bc-599">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="ba8bc-600">Sicurezza di Azure: frame di sicurezza: convalida dell'input | Attenuazioni</span><span class="sxs-lookup"><span data-stu-id="ba8bc-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="ba8bc-601">Modelli di progettazione cloud di Azure: modello di chiave del posteggiatore</span><span class="sxs-lookup"><span data-stu-id="ba8bc-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
