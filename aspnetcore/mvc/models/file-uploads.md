---
title: Caricare file in ASP.NET Core
author: rick-anderson
description: Come usare l'associazione del modello e lo streaming per caricare i file in ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/03/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/file-uploads
ms.openlocfilehash: 0da9e124b884337c63dd91b06df60ef7ca89cf3e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774132"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="3c2c4-103">Caricare file in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c2c4-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="3c2c4-104">Di [Steve Smith](https://ardalis.com/) e [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="3c2c4-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3c2c4-105">ASP.NET Core supporta il caricamento di uno o più file mediante l'associazione di modelli memorizzati nel buffer per i file più piccoli e il flusso non memorizzato nel buffer per file di dimensioni maggiori.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="3c2c4-106">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3c2c4-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="3c2c4-107">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="3c2c4-107">Security considerations</span></span>

<span data-ttu-id="3c2c4-108">Prestare attenzione quando si fornisce agli utenti la possibilità di caricare file in un server.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="3c2c4-109">Gli utenti malintenzionati possono tentare di:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="3c2c4-110">Eseguire attacchi [di tipo Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="3c2c4-111">Caricare virus o malware.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="3c2c4-112">Compromettere le reti e i server in altri modi.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="3c2c4-113">I passaggi di sicurezza che riducono la probabilità di un attacco riuscito sono:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="3c2c4-114">Caricare i file in un'area di caricamento file dedicata, preferibilmente in un'unità non di sistema.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="3c2c4-115">Un percorso dedicato semplifica l'imposizione delle restrizioni di sicurezza sui file caricati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="3c2c4-116">Disabilitare le autorizzazioni di esecuzione per il percorso di caricamento del file.&dagger;</span><span class="sxs-lookup"><span data-stu-id="3c2c4-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="3c2c4-117">Non **rende permanente i** file caricati nella stessa struttura di directory dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="3c2c4-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="3c2c4-118">Usare un nome file sicuro determinato dall'app.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="3c2c4-119">Non usare un nome file fornito dall'utente o il nome file non attendibile del file caricato. &dagger; HTML codifica il nome file non attendibile quando viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="3c2c4-120">Ad esempio, registrare il nome del file o visualizzarlo nell'Razor interfaccia utente (l'output viene codificato automaticamente in HTML).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="3c2c4-121">Consenti solo le estensioni di file approvate per la specifica di progettazione dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="3c2c4-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="3c2c4-122">Verificare che i controlli lato client vengano eseguiti sul server. &dagger; I controlli lato client sono facili da aggirare.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="3c2c4-123">Controllare le dimensioni di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="3c2c4-124">Impostare un limite di dimensioni massime per impedire caricamenti di grandi dimensioni.&dagger;</span><span class="sxs-lookup"><span data-stu-id="3c2c4-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="3c2c4-125">Quando i file non devono essere sovrascritti da un file caricato con lo stesso nome, controllare il nome del file sul database o sull'archiviazione fisica prima di caricare il file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="3c2c4-126">**Eseguire uno scanner virus/malware sul contenuto caricato prima che il file venga archiviato.**</span><span class="sxs-lookup"><span data-stu-id="3c2c4-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="3c2c4-127">&dagger;L'app di esempio illustra un approccio che soddisfa i criteri.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="3c2c4-128">Il caricamento di codice dannoso in un sistema è spesso il primo passaggio per l'esecuzione di codice in grado di:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="3c2c4-129">Ottenere il controllo completo di un sistema.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="3c2c4-130">Sovraccaricare un sistema con il risultato che si verifica un arresto anomalo del sistema.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="3c2c4-131">Compromettere i dati di sistemi o utenti.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="3c2c4-132">Applicare i graffiti a un'interfaccia utente pubblica.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="3c2c4-133">Per informazioni sulla riduzione della superficie di attacco quando si accettano file dagli utenti, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="3c2c4-134">Caricamento di file senza restrizioni</span><span class="sxs-lookup"><span data-stu-id="3c2c4-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="3c2c4-135">Protezione di Azure: Verificare i controlli appropriati quando si accettano file dagli utenti</span><span class="sxs-lookup"><span data-stu-id="3c2c4-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="3c2c4-136">Per ulteriori informazioni sull'implementazione di misure di sicurezza, inclusi esempi dall'app di esempio, vedere la sezione [convalida](#validation) .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="3c2c4-137">Scenari di archiviazione</span><span class="sxs-lookup"><span data-stu-id="3c2c4-137">Storage scenarios</span></span>

<span data-ttu-id="3c2c4-138">Le opzioni di archiviazione comuni per i file includono:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-138">Common storage options for files include:</span></span>

* <span data-ttu-id="3c2c4-139">Database</span><span class="sxs-lookup"><span data-stu-id="3c2c4-139">Database</span></span>

  * <span data-ttu-id="3c2c4-140">Per i caricamenti di file di piccole dimensioni, un database è spesso più veloce rispetto alle opzioni di archiviazione fisica (file system o condivisione di rete).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="3c2c4-141">Un database è spesso più pratico delle opzioni di archiviazione fisica perché il recupero di un record di database per i dati utente può fornire simultaneamente il contenuto del file, ad esempio un'immagine avatar.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="3c2c4-142">Un database è potenzialmente meno costoso rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="3c2c4-143">Archiviazione fisica (file system o condivisione di rete)</span><span class="sxs-lookup"><span data-stu-id="3c2c4-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="3c2c4-144">Per i caricamenti di file di grandi dimensioni:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-144">For large file uploads:</span></span>
    * <span data-ttu-id="3c2c4-145">I limiti del database possono limitare le dimensioni del caricamento.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="3c2c4-146">L'archiviazione fisica è spesso meno economica rispetto all'archiviazione in un database.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="3c2c4-147">L'archiviazione fisica è potenzialmente meno costosa rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="3c2c4-148">Il processo dell'app deve avere le autorizzazioni di lettura e scrittura per il percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="3c2c4-149">**Non concedere mai l'autorizzazione Execute.**</span><span class="sxs-lookup"><span data-stu-id="3c2c4-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="3c2c4-150">Servizio di archiviazione dei dati (ad esempio, [archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="3c2c4-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="3c2c4-151">I servizi in genere offrono una maggiore scalabilità e resilienza rispetto a soluzioni locali che in genere sono soggette a singoli punti di errore.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="3c2c4-152">I servizi sono un costo potenzialmente inferiore in scenari di infrastruttura di archiviazione di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="3c2c4-153">Per altre informazioni, vedere [Guida introduttiva: usare .NET per creare un BLOB nell'archivio oggetti](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="3c2c4-154">Scenari di caricamento di file</span><span class="sxs-lookup"><span data-stu-id="3c2c4-154">File upload scenarios</span></span>

<span data-ttu-id="3c2c4-155">Due approcci generali per il caricamento di file sono il buffering e il flusso.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="3c2c4-156">**responseBuffering**</span><span class="sxs-lookup"><span data-stu-id="3c2c4-156">**Buffering**</span></span>

<span data-ttu-id="3c2c4-157">L'intero file viene letto in un <xref:Microsoft.AspNetCore.Http.IFormFile>oggetto, ovvero una rappresentazione C# del file usato per elaborare o salvare il file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="3c2c4-158">Le risorse (disco, memoria) utilizzate dai caricamenti di file dipendono dal numero e dalle dimensioni dei caricamenti di file simultanei.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="3c2c4-159">Se un'app tenta di memorizzare nel buffer un numero eccessivo di caricamenti, il sito si arresta in modo anomalo quando esaurisce la memoria o lo spazio su disco.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="3c2c4-160">Se le dimensioni o la frequenza dei caricamenti di file esauriscono le risorse dell'app, usare il flusso.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="3c2c4-161">Ogni singolo file memorizzato nel buffer che supera 64 KB viene spostato dalla memoria in un file temporaneo su disco.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="3c2c4-162">Il buffering di file di piccole dimensioni viene trattato nelle sezioni seguenti di questo argomento:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="3c2c4-163">Archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="3c2c4-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="3c2c4-164">Database</span><span class="sxs-lookup"><span data-stu-id="3c2c4-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="3c2c4-165">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="3c2c4-165">**Streaming**</span></span>

<span data-ttu-id="3c2c4-166">Il file viene ricevuto da una richiesta multipart ed elaborato o salvato direttamente dall'app.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="3c2c4-167">Il flusso non migliora significativamente le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="3c2c4-168">Il flusso riduce le richieste di memoria o di spazio su disco durante il caricamento dei file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="3c2c4-169">Il flusso di file di grandi dimensioni è trattato nella sezione [caricare file di grandi dimensioni con flusso](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="3c2c4-170">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer all'archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="3c2c4-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="3c2c4-171">Per caricare file di piccole dimensioni, usare un modulo multipart o creare una richiesta POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="3c2c4-172">L'esempio seguente illustra l'uso di un Razor form di pagine per caricare un singolo file (*pages/BufferedSingleFileUploadPhysical. cshtml* nell'app di esempio):</span><span class="sxs-lookup"><span data-stu-id="3c2c4-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="3c2c4-173">L'esempio seguente è analogo all'esempio precedente, ad eccezione del fatto che:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="3c2c4-174">Per inviare i dati del modulo, viene usato JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="3c2c4-175">Nessuna convalida.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-175">There's no validation.</span></span>

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

<span data-ttu-id="3c2c4-176">Per eseguire il POST del form in JavaScript per i client che [non supportano l'API fetch](https://caniuse.com/#feat=fetch), usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="3c2c4-177">Usare un riempimento di recupero (ad esempio, [Window. fetch Refill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="3c2c4-178">Usare `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="3c2c4-179">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-179">For example:</span></span>

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

<span data-ttu-id="3c2c4-180">Per supportare il caricamento di file, i moduli HTML devono specificare un tipo di codifica (`enctype`) di `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="3c2c4-181">Affinché un `files` elemento input supporti il caricamento di più file, fornire l' `multiple` attributo nell' `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="3c2c4-182">È possibile accedere ai singoli file caricati nel server tramite l' [associazione](xref:mvc/models/model-binding) di modelli <xref:Microsoft.AspNetCore.Http.IFormFile>tramite.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="3c2c4-183">L'app di esempio illustra più caricamenti di file memorizzati nel buffer per scenari di archiviazione di database e fisici.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="3c2c4-184">Non **utilizzare la** proprietà `FileName` di <xref:Microsoft.AspNetCore.Http.IFormFile> diverso da per la visualizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="3c2c4-185">Durante la visualizzazione o la registrazione, HTML codifica il nome del file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="3c2c4-186">Un utente malintenzionato può fornire un nome file dannoso, inclusi percorsi completi o percorsi relativi.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="3c2c4-187">Le applicazioni devono:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-187">Applications should:</span></span>
>
> * <span data-ttu-id="3c2c4-188">Rimuovere il percorso dal nome file specificato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="3c2c4-189">Salvare il nome file con codifica HTML, percorso-rimosso per l'interfaccia utente o la registrazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="3c2c4-190">Genera un nuovo nome file casuale per l'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="3c2c4-191">Il codice seguente rimuove il percorso dal nome del file:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="3c2c4-192">Gli esempi forniti finora non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="3c2c4-193">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="3c2c4-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="3c2c4-194">Considerazioni sulla sicurezza</span><span class="sxs-lookup"><span data-stu-id="3c2c4-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="3c2c4-195">Validation</span><span class="sxs-lookup"><span data-stu-id="3c2c4-195">Validation</span></span>](#validation)

<span data-ttu-id="3c2c4-196">Quando si caricano file usando l'associazione di <xref:Microsoft.AspNetCore.Http.IFormFile>modelli e, il metodo di azione può accettare:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="3c2c4-197">Singolo <xref:Microsoft.AspNetCore.Http.IFormFile>oggetto.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="3c2c4-198">Qualsiasi raccolta seguente che rappresenta diversi file:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="3c2c4-199">[Elenco](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="3c2c4-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="3c2c4-200">L'associazione corrisponde ai file di form in base al nome.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-200">Binding matches form files by name.</span></span> <span data-ttu-id="3c2c4-201">Ad esempio, il valore `name` HTML in `<input type="file" name="formFile">` deve corrispondere al parametro/proprietà di C# associato`FormFile`().</span><span class="sxs-lookup"><span data-stu-id="3c2c4-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="3c2c4-202">Per ulteriori informazioni, vedere la sezione [corrispondenza del nome dell'attributo del nome del parametro del metodo post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="3c2c4-203">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-203">The following example:</span></span>

* <span data-ttu-id="3c2c4-204">Esegue il ciclo di uno o più file caricati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="3c2c4-205">USA [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per restituire un percorso completo per un file, incluso il nome del file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="3c2c4-206">Salva i file nella file system locale usando un nome file generato dall'app.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="3c2c4-207">Restituisce il numero totale e le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="3c2c4-208">Utilizzare `Path.GetRandomFileName` per generare un nome file senza percorso.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="3c2c4-209">Nell'esempio seguente il percorso viene ottenuto dalla configurazione:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="3c2c4-210">Il percorso passato a <xref:System.IO.FileStream> *deve* includere il nome del file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="3c2c4-211">Se il nome file non viene specificato, <xref:System.UnauthorizedAccessException> viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="3c2c4-212">I file caricati con <xref:Microsoft.AspNetCore.Http.IFormFile> la tecnica vengono memorizzati nel buffer in memoria o su disco sul server prima dell'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="3c2c4-213">All'interno del metodo di azione <xref:Microsoft.AspNetCore.Http.IFormFile> , il contenuto è accessibile <xref:System.IO.Stream>come.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="3c2c4-214">Oltre al file system locale, i file possono essere salvati in una condivisione di rete o in un servizio di archiviazione file, ad esempio [archiviazione BLOB di Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="3c2c4-215">Per un altro esempio che esegue il ciclo su più file per il caricamento e usa nomi di file sicuri, vedere *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="3c2c4-216">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) genera un' <xref:System.IO.IOException> eccezione se vengono creati più di 65.535 file senza eliminare i file temporanei precedenti.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="3c2c4-217">Il limite di 65.535 file è un limite per server.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="3c2c4-218">Per ulteriori informazioni su questo limite per il sistema operativo Windows, vedere la sezione Osservazioni negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="3c2c4-219">GetTempFileNameA (funzione)</span><span class="sxs-lookup"><span data-stu-id="3c2c4-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="3c2c4-220">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer in un database</span><span class="sxs-lookup"><span data-stu-id="3c2c4-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="3c2c4-221">Per archiviare i dati dei file binari in un database utilizzando [Entity Framework](/ef/core/index), <xref:System.Byte> definire una proprietà di matrice nell'entità:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="3c2c4-222">Specificare una proprietà del modello di pagina per la classe che <xref:Microsoft.AspNetCore.Http.IFormFile>include:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="3c2c4-223"><xref:Microsoft.AspNetCore.Http.IFormFile>può essere usato direttamente come parametro del metodo di azione o come proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="3c2c4-224">Nell'esempio precedente viene utilizzata una proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="3c2c4-225">Il `FileUpload` viene utilizzato nel form Razor pagine:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="3c2c4-226">Quando il modulo viene inviato al server, copiarlo <xref:Microsoft.AspNetCore.Http.IFormFile> in un flusso e salvarlo come una matrice di byte nel database.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="3c2c4-227">Nell'esempio seguente, `_dbContext` archivia il contesto di database dell'app:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="3c2c4-228">L'esempio precedente è simile a uno scenario illustrato nell'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="3c2c4-229">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3c2c4-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="3c2c4-230">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="3c2c4-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="3c2c4-231">Quando si archiviano dati binari all'interno di database relazionali, è necessario prestare attenzione, perché l'operazione può influire negativamente sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="3c2c4-232">Non fare affidamento o considerare `FileName` attendibile <xref:Microsoft.AspNetCore.Http.IFormFile> la proprietà di senza convalida.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="3c2c4-233">La `FileName` proprietà deve essere usata solo a scopo di visualizzazione e solo dopo la codifica HTML.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="3c2c4-234">Gli esempi forniti non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="3c2c4-235">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="3c2c4-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="3c2c4-236">Considerazioni sulla sicurezza</span><span class="sxs-lookup"><span data-stu-id="3c2c4-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="3c2c4-237">Validation</span><span class="sxs-lookup"><span data-stu-id="3c2c4-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="3c2c4-238">Caricare file di grandi dimensioni con flusso</span><span class="sxs-lookup"><span data-stu-id="3c2c4-238">Upload large files with streaming</span></span>

<span data-ttu-id="3c2c4-239">Nell'esempio seguente viene illustrato come utilizzare JavaScript per trasmettere un file a un'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="3c2c4-240">Il token antifalsificazione del file viene generato usando un attributo di filtro personalizzato e passato alle intestazioni HTTP del client anziché nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="3c2c4-241">Poiché il metodo di azione elabora direttamente i dati caricati, l'associazione del modello di modulo viene disabilitata da un altro filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="3c2c4-242">All'interno dell'azione, il contenuto del form viene letto tramite un `MultipartReader`, che legge ogni singola `MultipartSection`, elaborando il file o archiviandone il contenuto, come appropriato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="3c2c4-243">Una volta lette le sezioni multiparte, l'azione esegue una propria associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="3c2c4-244">La risposta della pagina iniziale carica il modulo e salva un token antifalsificazione in un cookie (tramite `GenerateAntiforgeryTokenCookieAttribute` l'attributo).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="3c2c4-245">L'attributo usa il [supporto antifalsificazione](xref:security/anti-request-forgery) predefinito di ASP.NET Core per impostare un cookie con un token di richiesta:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="3c2c4-246">Viene `DisableFormValueModelBindingAttribute` usato per disabilitare l'associazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="3c2c4-247">Nell'app di `GenerateAntiforgeryTokenCookieAttribute` esempio e `DisableFormValueModelBindingAttribute` vengono applicati come filtri ai modelli di applicazione della pagina di `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` all' `Startup.ConfigureServices` uso [ Razor delle convenzioni delle pagine](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="3c2c4-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="3c2c4-248">Poiché l'associazione di modelli non legge il modulo, i parametri associati al modulo non vengono associati (la query, la route e l'intestazione continuano a funzionare).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="3c2c4-249">Il metodo di azione funziona direttamente con `Request` la proprietà.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="3c2c4-250">Per leggere ogni sezione, viene usato un `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="3c2c4-251">I dati chiave/valore vengono archiviati in `KeyValueAccumulator`un oggetto.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="3c2c4-252">Una volta lette le sezioni multipart, il contenuto di `KeyValueAccumulator` viene utilizzato per associare i dati del modulo a un tipo di modello.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="3c2c4-253">Metodo completo `StreamingController.UploadDatabase` per lo streaming in un database con EF Core:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="3c2c4-254">`MultipartRequestHelper`(*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="3c2c4-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="3c2c4-255">Il metodo `StreamingController.UploadPhysical` completo per lo streaming in una posizione fisica:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="3c2c4-256">Nell'app di esempio, i controlli di convalida vengono `FileHelpers.ProcessStreamedFile`gestiti da.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="3c2c4-257">Convalida</span><span class="sxs-lookup"><span data-stu-id="3c2c4-257">Validation</span></span>

<span data-ttu-id="3c2c4-258">La classe dell'app `FileHelpers` di esempio illustra alcuni controlli per i caricamenti di file memorizzati nel buffer <xref:Microsoft.AspNetCore.Http.IFormFile> e in flusso.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="3c2c4-259">Per l' <xref:Microsoft.AspNetCore.Http.IFormFile> elaborazione dei caricamenti di file memorizzati nel buffer nell'app di esempio `ProcessFormFile` , vedere il metodo nel file *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="3c2c4-260">Per l'elaborazione di file trasmessi, vedere `ProcessStreamedFile` il metodo nello stesso file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="3c2c4-261">I metodi di elaborazione della convalida illustrati nell'app di esempio non analizzano il contenuto dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="3c2c4-262">Nella maggior parte degli scenari di produzione, nel file viene usata un'API scanner di virus/malware prima di rendere disponibile il file per gli utenti o altri sistemi.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="3c2c4-263">Sebbene l'esempio di argomento fornisca un esempio funzionante di tecniche di convalida, non `FileHelpers` implementare la classe in un'app di produzione a meno che non si:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="3c2c4-264">Comprendere completamente l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="3c2c4-265">Modificare l'implementazione in modo appropriato per l'ambiente e le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="3c2c4-266">**Non implementare mai indiscriminatamente il codice di sicurezza in un'app senza soddisfare questi requisiti.**</span><span class="sxs-lookup"><span data-stu-id="3c2c4-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="3c2c4-267">Convalida contenuto</span><span class="sxs-lookup"><span data-stu-id="3c2c4-267">Content validation</span></span>

<span data-ttu-id="3c2c4-268">**Usare un'API di analisi di virus/malware di terze parti nel contenuto caricato.**</span><span class="sxs-lookup"><span data-stu-id="3c2c4-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="3c2c4-269">L'analisi dei file richiede le risorse del server in scenari con volumi elevati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="3c2c4-270">Se le prestazioni di elaborazione delle richieste sono diminuite a causa dell'analisi dei file, provare a eseguire l'offload del lavoro di analisi in un [servizio in background](xref:fundamentals/host/hosted-services), possibilmente in un servizio in esecuzione in un server diverso dal server dell'app.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="3c2c4-271">In genere, i file caricati vengono mantenuti in un'area in quarantena finché non vengono controllati dal programma antivirus in background.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="3c2c4-272">Quando un file passa, il file viene spostato nel percorso di archiviazione file normale.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="3c2c4-273">Questi passaggi vengono in genere eseguiti insieme a un record di database che indica lo stato di analisi di un file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="3c2c4-274">Usando un approccio di questo tipo, l'app e il server app rimangono incentrati sulla risposta alle richieste.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="3c2c4-275">Convalida dell'estensione di file</span><span class="sxs-lookup"><span data-stu-id="3c2c4-275">File extension validation</span></span>

<span data-ttu-id="3c2c4-276">L'estensione del file caricato deve essere verificata rispetto a un elenco di estensioni consentite.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="3c2c4-277">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="3c2c4-278">Convalida della firma del file</span><span class="sxs-lookup"><span data-stu-id="3c2c4-278">File signature validation</span></span>

<span data-ttu-id="3c2c4-279">La firma di un file è determinata dai primi byte all'inizio di un file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="3c2c4-280">Questi byte possono essere usati per indicare se l'estensione corrisponde al contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="3c2c4-281">L'app di esempio controlla le firme dei file per alcuni tipi di file comuni.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="3c2c4-282">Nell'esempio seguente, la firma del file per un'immagine JPEG viene verificata rispetto al file:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="3c2c4-283">Per ottenere ulteriori firme di file, vedere il [database delle firme file](https://www.filesignatures.net/) e le specifiche del file ufficiale.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="3c2c4-284">Sicurezza del nome file</span><span class="sxs-lookup"><span data-stu-id="3c2c4-284">File name security</span></span>

<span data-ttu-id="3c2c4-285">Non usare mai un nome file fornito dal client per salvare un file nell'archiviazione fisica.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="3c2c4-286">Creare un nome file sicuro per il file usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per creare un percorso completo (incluso il nome file) per l'archiviazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="3c2c4-287">codifica HTML automaticamente i valori delle proprietà per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-287"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="3c2c4-288">Il codice seguente è sicuro da usare:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="3c2c4-289">Al di Razorfuori di <xref:System.Net.WebUtility.HtmlEncode*> , sempre il contenuto del nome file da una richiesta dell'utente.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="3c2c4-290">Molte implementazioni devono includere un controllo per verificare che il file esista. in caso contrario, il file viene sovrascritto da un file con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="3c2c4-291">Fornire logica aggiuntiva per soddisfare le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="3c2c4-292">Convalida dimensioni</span><span class="sxs-lookup"><span data-stu-id="3c2c4-292">Size validation</span></span>

<span data-ttu-id="3c2c4-293">Limitare le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="3c2c4-294">Nell'app di esempio, le dimensioni del file sono limitate a 2 MB (indicate in byte).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="3c2c4-295">Il limite viene fornito tramite la [configurazione](xref:fundamentals/configuration/index) dal file *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="3c2c4-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="3c2c4-296">`FileSizeLimit` Viene inserito nelle `PageModel` classi:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="3c2c4-297">Quando le dimensioni di un file superano il limite, il file viene rifiutato:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="3c2c4-298">Corrisponde al valore dell'attributo Name al nome del parametro del metodo POST</span><span class="sxs-lookup"><span data-stu-id="3c2c4-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="3c2c4-299">In nonRazor form che inviano dati del modulo o usano `FormData` direttamente il nome di JavaScript, il nome specificato nell'elemento del `FormData` form o deve corrispondere al nome del parametro nell'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="3c2c4-300">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-300">In the following example:</span></span>

* <span data-ttu-id="3c2c4-301">Quando si usa `<input>` un elemento, `name` l'attributo viene impostato sul valore `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="3c2c4-302">Quando si `FormData` USA in JavaScript, il nome viene impostato sul valore `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="3c2c4-303">Usare un nome corrispondente per il parametro del metodo C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="3c2c4-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="3c2c4-304">Per un Razor metodo gestore pagina pagine denominato `Upload`:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="3c2c4-305">Per un metodo di azione POST controller MVC:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="3c2c4-306">Configurazione del server e dell'app</span><span class="sxs-lookup"><span data-stu-id="3c2c4-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="3c2c4-307">Limite di lunghezza del corpo multipart</span><span class="sxs-lookup"><span data-stu-id="3c2c4-307">Multipart body length limit</span></span>

<span data-ttu-id="3c2c4-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>imposta il limite per la lunghezza di ogni corpo multipart.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="3c2c4-309">Le sezioni del modulo che superano questo <xref:System.IO.InvalidDataException> limite generano un'espressione quando vengono analizzate.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="3c2c4-310">Il valore predefinito è 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="3c2c4-311">Personalizzare il limite usando l' <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> impostazione in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="3c2c4-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>viene usato per impostare <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="3c2c4-313">In un' Razor app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="3c2c4-314">In un' Razor app pagine o in un'app MVC, applicare il filtro al modello di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="3c2c4-315">Dimensioni massime del corpo della richiesta di gheppio</span><span class="sxs-lookup"><span data-stu-id="3c2c4-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="3c2c4-316">Per le app ospitate da gheppio, le dimensioni massime predefinite del corpo della richiesta sono pari a 30 milioni byte, ovvero circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="3c2c4-317">Personalizzare il limite usando l'opzione del server [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) gheppio:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="3c2c4-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>viene usato per impostare [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="3c2c4-319">In un' Razor app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="3c2c4-320">In un' Razor app pagine o in un'app MVC, applicare il filtro alla classe del gestore di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="3c2c4-321">Il `RequestSizeLimitAttribute` può essere applicato anche usando la [`@attribute`](xref:mvc/views/razor#attribute) Razor direttiva:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="3c2c4-322">Altri limiti del gheppio</span><span class="sxs-lookup"><span data-stu-id="3c2c4-322">Other Kestrel limits</span></span>

<span data-ttu-id="3c2c4-323">Per le app ospitate da gheppio possono essere applicati altri limiti di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="3c2c4-324">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="3c2c4-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="3c2c4-325">Frequenza dei dati di richiesta e risposta</span><span class="sxs-lookup"><span data-stu-id="3c2c4-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="3c2c4-326">Limite lunghezza contenuto IIS</span><span class="sxs-lookup"><span data-stu-id="3c2c4-326">IIS content length limit</span></span>

<span data-ttu-id="3c2c4-327">Il limite di richieste predefinito`maxAllowedContentLength`() è 30 milioni byte, che corrisponde a circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="3c2c4-328">Personalizzare il limite nel file *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="3c2c4-328">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="3c2c4-329">Questa impostazione si applica solo a IIS.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-329">This setting only applies to IIS.</span></span> <span data-ttu-id="3c2c4-330">Il comportamento non si verifica per impostazione predefinita con l'hosting in Kestrel.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="3c2c4-331">Per altre informazioni, vedere [limiti \<delle richieste requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="3c2c4-332">Le limitazioni del modulo ASP.NET Core o della presenza del modulo filtro richieste IIS possono limitare il caricamento a 2 o 4 GB.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="3c2c4-333">Per altre informazioni, vedere [non è possibile caricare file di dimensioni superiori a 2 GB (DotNet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="3c2c4-334">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="3c2c4-334">Troubleshoot</span></span>

<span data-ttu-id="3c2c4-335">Di seguito sono trattati alcuni problemi comuni riscontrati durante il caricamento di file, con le soluzioni possibili corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="3c2c4-336">Errore non trovato quando viene distribuito in un server IIS</span><span class="sxs-lookup"><span data-stu-id="3c2c4-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="3c2c4-337">L'errore seguente indica che il file caricato supera la lunghezza del contenuto configurata del server:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="3c2c4-338">Per ulteriori informazioni sull'aumento del limite, vedere la sezione [limite di lunghezza del contenuto IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="3c2c4-339">Errore di connessione</span><span class="sxs-lookup"><span data-stu-id="3c2c4-339">Connection failure</span></span>

<span data-ttu-id="3c2c4-340">Un errore di connessione e una connessione al server di reimpostazione probabilmente indicano che il file caricato supera le dimensioni massime del corpo della richiesta del gheppio.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="3c2c4-341">Per ulteriori informazioni, vedere la sezione relativa alle [dimensioni massime del corpo della richiesta di Gheppio](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="3c2c4-342">I limiti di connessione del client gheppio possono richiedere anche modifiche.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="3c2c4-343">Eccezione per riferimento Null con IFormFile</span><span class="sxs-lookup"><span data-stu-id="3c2c4-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="3c2c4-344">Se il <xref:Microsoft.AspNetCore.Http.IFormFile> controller accetta file caricati utilizzando, ma il valore è `null`, verificare che il form HTML specifichi un `enctype` valore di. `multipart/form-data`</span><span class="sxs-lookup"><span data-stu-id="3c2c4-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="3c2c4-345">Se questo attributo non è impostato sull' `<form>` elemento, il caricamento del file non viene eseguito e <xref:Microsoft.AspNetCore.Http.IFormFile> gli argomenti `null`associati sono.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="3c2c4-346">Verificare inoltre che la [denominazione di caricamento nei dati del modulo corrisponda alla denominazione dell'app](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="3c2c4-347">Il flusso è troppo lungo</span><span class="sxs-lookup"><span data-stu-id="3c2c4-347">Stream was too long</span></span>

<span data-ttu-id="3c2c4-348">Gli esempi in questo argomento si basano su <xref:System.IO.MemoryStream> per conservare il contenuto del file caricato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="3c2c4-349">Il limite delle dimensioni di `MemoryStream` un `int.MaxValue`oggetto è.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="3c2c4-350">Se lo scenario di caricamento dei file dell'app richiede un contenuto del file di dimensioni superiori a 50 MB, usare un approccio alternativo che `MemoryStream` non si basa su un singolo per contenere il contenuto di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3c2c4-351">ASP.NET Core supporta il caricamento di uno o più file mediante l'associazione di modelli memorizzati nel buffer per i file più piccoli e il flusso non memorizzato nel buffer per file di dimensioni maggiori.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="3c2c4-352">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3c2c4-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="3c2c4-353">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="3c2c4-353">Security considerations</span></span>

<span data-ttu-id="3c2c4-354">Prestare attenzione quando si fornisce agli utenti la possibilità di caricare file in un server.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="3c2c4-355">Gli utenti malintenzionati possono tentare di:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="3c2c4-356">Eseguire attacchi [di tipo Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="3c2c4-357">Caricare virus o malware.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="3c2c4-358">Compromettere le reti e i server in altri modi.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="3c2c4-359">I passaggi di sicurezza che riducono la probabilità di un attacco riuscito sono:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="3c2c4-360">Caricare i file in un'area di caricamento file dedicata, preferibilmente in un'unità non di sistema.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="3c2c4-361">Un percorso dedicato semplifica l'imposizione delle restrizioni di sicurezza sui file caricati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="3c2c4-362">Disabilitare le autorizzazioni di esecuzione per il percorso di caricamento del file.&dagger;</span><span class="sxs-lookup"><span data-stu-id="3c2c4-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="3c2c4-363">Non **rende permanente i** file caricati nella stessa struttura di directory dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="3c2c4-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="3c2c4-364">Usare un nome file sicuro determinato dall'app.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="3c2c4-365">Non usare un nome file fornito dall'utente o il nome file non attendibile del file caricato. &dagger; HTML codifica il nome file non attendibile quando viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="3c2c4-366">Ad esempio, registrare il nome del file o visualizzarlo nell'Razor interfaccia utente (l'output viene codificato automaticamente in HTML).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="3c2c4-367">Consenti solo le estensioni di file approvate per la specifica di progettazione dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="3c2c4-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="3c2c4-368">Verificare che i controlli lato client vengano eseguiti sul server. &dagger; I controlli lato client sono facili da aggirare.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="3c2c4-369">Controllare le dimensioni di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="3c2c4-370">Impostare un limite di dimensioni massime per impedire caricamenti di grandi dimensioni.&dagger;</span><span class="sxs-lookup"><span data-stu-id="3c2c4-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="3c2c4-371">Quando i file non devono essere sovrascritti da un file caricato con lo stesso nome, controllare il nome del file sul database o sull'archiviazione fisica prima di caricare il file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="3c2c4-372">**Eseguire uno scanner virus/malware sul contenuto caricato prima che il file venga archiviato.**</span><span class="sxs-lookup"><span data-stu-id="3c2c4-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="3c2c4-373">&dagger;L'app di esempio illustra un approccio che soddisfa i criteri.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="3c2c4-374">Il caricamento di codice dannoso in un sistema è spesso il primo passaggio per l'esecuzione di codice in grado di:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="3c2c4-375">Ottenere il controllo completo di un sistema.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="3c2c4-376">Sovraccaricare un sistema con il risultato che si verifica un arresto anomalo del sistema.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="3c2c4-377">Compromettere i dati di sistemi o utenti.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="3c2c4-378">Applicare i graffiti a un'interfaccia utente pubblica.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="3c2c4-379">Per informazioni sulla riduzione della superficie di attacco quando si accettano file dagli utenti, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="3c2c4-380">Caricamento di file senza restrizioni</span><span class="sxs-lookup"><span data-stu-id="3c2c4-380">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="3c2c4-381">Protezione di Azure: Verificare i controlli appropriati quando si accettano file dagli utenti</span><span class="sxs-lookup"><span data-stu-id="3c2c4-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="3c2c4-382">Per ulteriori informazioni sull'implementazione di misure di sicurezza, inclusi esempi dall'app di esempio, vedere la sezione [convalida](#validation) .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="3c2c4-383">Scenari di archiviazione</span><span class="sxs-lookup"><span data-stu-id="3c2c4-383">Storage scenarios</span></span>

<span data-ttu-id="3c2c4-384">Le opzioni di archiviazione comuni per i file includono:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-384">Common storage options for files include:</span></span>

* <span data-ttu-id="3c2c4-385">Database</span><span class="sxs-lookup"><span data-stu-id="3c2c4-385">Database</span></span>

  * <span data-ttu-id="3c2c4-386">Per i caricamenti di file di piccole dimensioni, un database è spesso più veloce rispetto alle opzioni di archiviazione fisica (file system o condivisione di rete).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="3c2c4-387">Un database è spesso più pratico delle opzioni di archiviazione fisica perché il recupero di un record di database per i dati utente può fornire simultaneamente il contenuto del file, ad esempio un'immagine avatar.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="3c2c4-388">Un database è potenzialmente meno costoso rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="3c2c4-389">Archiviazione fisica (file system o condivisione di rete)</span><span class="sxs-lookup"><span data-stu-id="3c2c4-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="3c2c4-390">Per i caricamenti di file di grandi dimensioni:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-390">For large file uploads:</span></span>
    * <span data-ttu-id="3c2c4-391">I limiti del database possono limitare le dimensioni del caricamento.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="3c2c4-392">L'archiviazione fisica è spesso meno economica rispetto all'archiviazione in un database.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="3c2c4-393">L'archiviazione fisica è potenzialmente meno costosa rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="3c2c4-394">Il processo dell'app deve avere le autorizzazioni di lettura e scrittura per il percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="3c2c4-395">**Non concedere mai l'autorizzazione Execute.**</span><span class="sxs-lookup"><span data-stu-id="3c2c4-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="3c2c4-396">Servizio di archiviazione dei dati (ad esempio, [archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="3c2c4-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="3c2c4-397">I servizi in genere offrono una maggiore scalabilità e resilienza rispetto a soluzioni locali che in genere sono soggette a singoli punti di errore.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="3c2c4-398">I servizi sono un costo potenzialmente inferiore in scenari di infrastruttura di archiviazione di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="3c2c4-399">Per altre informazioni, vedere [Guida introduttiva: usare .NET per creare un BLOB nell'archivio oggetti](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="3c2c4-400">Nell'argomento viene <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>illustrato, <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> ma è possibile utilizzare per salvare <xref:System.IO.FileStream> un nell'archivio BLOB quando si utilizza <xref:System.IO.Stream>un.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="3c2c4-401">Scenari di caricamento di file</span><span class="sxs-lookup"><span data-stu-id="3c2c4-401">File upload scenarios</span></span>

<span data-ttu-id="3c2c4-402">Due approcci generali per il caricamento di file sono il buffering e il flusso.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="3c2c4-403">**responseBuffering**</span><span class="sxs-lookup"><span data-stu-id="3c2c4-403">**Buffering**</span></span>

<span data-ttu-id="3c2c4-404">L'intero file viene letto in un <xref:Microsoft.AspNetCore.Http.IFormFile>oggetto, ovvero una rappresentazione C# del file usato per elaborare o salvare il file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="3c2c4-405">Le risorse (disco, memoria) utilizzate dai caricamenti di file dipendono dal numero e dalle dimensioni dei caricamenti di file simultanei.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="3c2c4-406">Se un'app tenta di memorizzare nel buffer un numero eccessivo di caricamenti, il sito si arresta in modo anomalo quando esaurisce la memoria o lo spazio su disco.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="3c2c4-407">Se le dimensioni o la frequenza dei caricamenti di file esauriscono le risorse dell'app, usare il flusso.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="3c2c4-408">Ogni singolo file memorizzato nel buffer che supera 64 KB viene spostato dalla memoria in un file temporaneo su disco.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="3c2c4-409">Il buffering di file di piccole dimensioni viene trattato nelle sezioni seguenti di questo argomento:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="3c2c4-410">Archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="3c2c4-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="3c2c4-411">Database</span><span class="sxs-lookup"><span data-stu-id="3c2c4-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="3c2c4-412">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="3c2c4-412">**Streaming**</span></span>

<span data-ttu-id="3c2c4-413">Il file viene ricevuto da una richiesta multipart ed elaborato o salvato direttamente dall'app.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="3c2c4-414">Il flusso non migliora significativamente le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="3c2c4-415">Il flusso riduce le richieste di memoria o di spazio su disco durante il caricamento dei file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="3c2c4-416">Il flusso di file di grandi dimensioni è trattato nella sezione [caricare file di grandi dimensioni con flusso](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="3c2c4-417">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer all'archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="3c2c4-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="3c2c4-418">Per caricare file di piccole dimensioni, usare un modulo multipart o creare una richiesta POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="3c2c4-419">L'esempio seguente illustra l'uso di un Razor form di pagine per caricare un singolo file (*pages/BufferedSingleFileUploadPhysical. cshtml* nell'app di esempio):</span><span class="sxs-lookup"><span data-stu-id="3c2c4-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="3c2c4-420">L'esempio seguente è analogo all'esempio precedente, ad eccezione del fatto che:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="3c2c4-421">Per inviare i dati del modulo, viene usato JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="3c2c4-422">Nessuna convalida.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-422">There's no validation.</span></span>

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

<span data-ttu-id="3c2c4-423">Per eseguire il POST del form in JavaScript per i client che [non supportano l'API fetch](https://caniuse.com/#feat=fetch), usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="3c2c4-424">Usare un riempimento di recupero (ad esempio, [Window. fetch Refill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="3c2c4-425">Usare `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="3c2c4-426">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-426">For example:</span></span>

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

<span data-ttu-id="3c2c4-427">Per supportare il caricamento di file, i moduli HTML devono specificare un tipo di codifica (`enctype`) di `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="3c2c4-428">Affinché un `files` elemento input supporti il caricamento di più file, fornire l' `multiple` attributo nell' `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="3c2c4-429">È possibile accedere ai singoli file caricati nel server tramite l' [associazione](xref:mvc/models/model-binding) di modelli <xref:Microsoft.AspNetCore.Http.IFormFile>tramite.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="3c2c4-430">L'app di esempio illustra più caricamenti di file memorizzati nel buffer per scenari di archiviazione di database e fisici.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="3c2c4-431">Non **utilizzare la** proprietà `FileName` di <xref:Microsoft.AspNetCore.Http.IFormFile> diverso da per la visualizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="3c2c4-432">Durante la visualizzazione o la registrazione, HTML codifica il nome del file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="3c2c4-433">Un utente malintenzionato può fornire un nome file dannoso, inclusi percorsi completi o percorsi relativi.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="3c2c4-434">Le applicazioni devono:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-434">Applications should:</span></span>
>
> * <span data-ttu-id="3c2c4-435">Rimuovere il percorso dal nome file specificato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="3c2c4-436">Salvare il nome file con codifica HTML, percorso-rimosso per l'interfaccia utente o la registrazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="3c2c4-437">Genera un nuovo nome file casuale per l'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="3c2c4-438">Il codice seguente rimuove il percorso dal nome del file:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="3c2c4-439">Gli esempi forniti finora non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="3c2c4-440">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="3c2c4-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="3c2c4-441">Considerazioni sulla sicurezza</span><span class="sxs-lookup"><span data-stu-id="3c2c4-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="3c2c4-442">Validation</span><span class="sxs-lookup"><span data-stu-id="3c2c4-442">Validation</span></span>](#validation)

<span data-ttu-id="3c2c4-443">Quando si caricano file usando l'associazione di <xref:Microsoft.AspNetCore.Http.IFormFile>modelli e, il metodo di azione può accettare:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="3c2c4-444">Singolo <xref:Microsoft.AspNetCore.Http.IFormFile>oggetto.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="3c2c4-445">Qualsiasi raccolta seguente che rappresenta diversi file:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="3c2c4-446">[Elenco](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="3c2c4-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="3c2c4-447">L'associazione corrisponde ai file di form in base al nome.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-447">Binding matches form files by name.</span></span> <span data-ttu-id="3c2c4-448">Ad esempio, il valore `name` HTML in `<input type="file" name="formFile">` deve corrispondere al parametro/proprietà di C# associato`FormFile`().</span><span class="sxs-lookup"><span data-stu-id="3c2c4-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="3c2c4-449">Per ulteriori informazioni, vedere la sezione [corrispondenza del nome dell'attributo del nome del parametro del metodo post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="3c2c4-450">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-450">The following example:</span></span>

* <span data-ttu-id="3c2c4-451">Esegue il ciclo di uno o più file caricati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="3c2c4-452">USA [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per restituire un percorso completo per un file, incluso il nome del file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="3c2c4-453">Salva i file nella file system locale usando un nome file generato dall'app.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="3c2c4-454">Restituisce il numero totale e le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-454">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="3c2c4-455">Utilizzare `Path.GetRandomFileName` per generare un nome file senza percorso.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="3c2c4-456">Nell'esempio seguente il percorso viene ottenuto dalla configurazione:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-456">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="3c2c4-457">Il percorso passato a <xref:System.IO.FileStream> *deve* includere il nome del file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="3c2c4-458">Se il nome file non viene specificato, <xref:System.UnauthorizedAccessException> viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="3c2c4-459">I file caricati con <xref:Microsoft.AspNetCore.Http.IFormFile> la tecnica vengono memorizzati nel buffer in memoria o su disco sul server prima dell'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="3c2c4-460">All'interno del metodo di azione <xref:Microsoft.AspNetCore.Http.IFormFile> , il contenuto è accessibile <xref:System.IO.Stream>come.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="3c2c4-461">Oltre al file system locale, i file possono essere salvati in una condivisione di rete o in un servizio di archiviazione file, ad esempio [archiviazione BLOB di Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="3c2c4-462">Per un altro esempio che esegue il ciclo su più file per il caricamento e usa nomi di file sicuri, vedere *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="3c2c4-463">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) genera un' <xref:System.IO.IOException> eccezione se vengono creati più di 65.535 file senza eliminare i file temporanei precedenti.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="3c2c4-464">Il limite di 65.535 file è un limite per server.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="3c2c4-465">Per ulteriori informazioni su questo limite per il sistema operativo Windows, vedere la sezione Osservazioni negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="3c2c4-466">GetTempFileNameA (funzione)</span><span class="sxs-lookup"><span data-stu-id="3c2c4-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="3c2c4-467">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer in un database</span><span class="sxs-lookup"><span data-stu-id="3c2c4-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="3c2c4-468">Per archiviare i dati dei file binari in un database utilizzando [Entity Framework](/ef/core/index), <xref:System.Byte> definire una proprietà di matrice nell'entità:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="3c2c4-469">Specificare una proprietà del modello di pagina per la classe che <xref:Microsoft.AspNetCore.Http.IFormFile>include:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="3c2c4-470"><xref:Microsoft.AspNetCore.Http.IFormFile>può essere usato direttamente come parametro del metodo di azione o come proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="3c2c4-471">Nell'esempio precedente viene utilizzata una proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="3c2c4-472">Il `FileUpload` viene utilizzato nel form Razor pagine:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-472">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="3c2c4-473">Quando il modulo viene inviato al server, copiarlo <xref:Microsoft.AspNetCore.Http.IFormFile> in un flusso e salvarlo come una matrice di byte nel database.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="3c2c4-474">Nell'esempio seguente, `_dbContext` archivia il contesto di database dell'app:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-474">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="3c2c4-475">L'esempio precedente è simile a uno scenario illustrato nell'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="3c2c4-476">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3c2c4-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="3c2c4-477">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="3c2c4-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="3c2c4-478">Quando si archiviano dati binari all'interno di database relazionali, è necessario prestare attenzione, perché l'operazione può influire negativamente sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="3c2c4-479">Non fare affidamento o considerare `FileName` attendibile <xref:Microsoft.AspNetCore.Http.IFormFile> la proprietà di senza convalida.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="3c2c4-480">La `FileName` proprietà deve essere usata solo a scopo di visualizzazione e solo dopo la codifica HTML.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="3c2c4-481">Gli esempi forniti non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="3c2c4-482">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="3c2c4-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="3c2c4-483">Considerazioni sulla sicurezza</span><span class="sxs-lookup"><span data-stu-id="3c2c4-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="3c2c4-484">Validation</span><span class="sxs-lookup"><span data-stu-id="3c2c4-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="3c2c4-485">Caricare file di grandi dimensioni con flusso</span><span class="sxs-lookup"><span data-stu-id="3c2c4-485">Upload large files with streaming</span></span>

<span data-ttu-id="3c2c4-486">Nell'esempio seguente viene illustrato come utilizzare JavaScript per trasmettere un file a un'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="3c2c4-487">Il token antifalsificazione del file viene generato usando un attributo di filtro personalizzato e passato alle intestazioni HTTP del client anziché nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="3c2c4-488">Poiché il metodo di azione elabora direttamente i dati caricati, l'associazione del modello di modulo viene disabilitata da un altro filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="3c2c4-489">All'interno dell'azione, il contenuto del form viene letto tramite un `MultipartReader`, che legge ogni singola `MultipartSection`, elaborando il file o archiviandone il contenuto, come appropriato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="3c2c4-490">Una volta lette le sezioni multiparte, l'azione esegue una propria associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="3c2c4-491">La risposta della pagina iniziale carica il modulo e salva un token antifalsificazione in un cookie (tramite `GenerateAntiforgeryTokenCookieAttribute` l'attributo).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="3c2c4-492">L'attributo usa il [supporto antifalsificazione](xref:security/anti-request-forgery) predefinito di ASP.NET Core per impostare un cookie con un token di richiesta:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="3c2c4-493">Viene `DisableFormValueModelBindingAttribute` usato per disabilitare l'associazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="3c2c4-494">Nell'app di `GenerateAntiforgeryTokenCookieAttribute` esempio e `DisableFormValueModelBindingAttribute` vengono applicati come filtri ai modelli di applicazione della pagina di `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` all' `Startup.ConfigureServices` uso [ Razor delle convenzioni delle pagine](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="3c2c4-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="3c2c4-495">Poiché l'associazione di modelli non legge il modulo, i parametri associati al modulo non vengono associati (la query, la route e l'intestazione continuano a funzionare).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="3c2c4-496">Il metodo di azione funziona direttamente con `Request` la proprietà.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="3c2c4-497">Per leggere ogni sezione, viene usato un `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="3c2c4-498">I dati chiave/valore vengono archiviati in `KeyValueAccumulator`un oggetto.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="3c2c4-499">Una volta lette le sezioni multipart, il contenuto di `KeyValueAccumulator` viene utilizzato per associare i dati del modulo a un tipo di modello.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="3c2c4-500">Metodo completo `StreamingController.UploadDatabase` per lo streaming in un database con EF Core:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="3c2c4-501">`MultipartRequestHelper`(*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="3c2c4-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="3c2c4-502">Il metodo `StreamingController.UploadPhysical` completo per lo streaming in una posizione fisica:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="3c2c4-503">Nell'app di esempio, i controlli di convalida vengono `FileHelpers.ProcessStreamedFile`gestiti da.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="3c2c4-504">Convalida</span><span class="sxs-lookup"><span data-stu-id="3c2c4-504">Validation</span></span>

<span data-ttu-id="3c2c4-505">La classe dell'app `FileHelpers` di esempio illustra alcuni controlli per i caricamenti di file memorizzati nel buffer <xref:Microsoft.AspNetCore.Http.IFormFile> e in flusso.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="3c2c4-506">Per l' <xref:Microsoft.AspNetCore.Http.IFormFile> elaborazione dei caricamenti di file memorizzati nel buffer nell'app di esempio `ProcessFormFile` , vedere il metodo nel file *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="3c2c4-507">Per l'elaborazione di file trasmessi, vedere `ProcessStreamedFile` il metodo nello stesso file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="3c2c4-508">I metodi di elaborazione della convalida illustrati nell'app di esempio non analizzano il contenuto dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="3c2c4-509">Nella maggior parte degli scenari di produzione, nel file viene usata un'API scanner di virus/malware prima di rendere disponibile il file per gli utenti o altri sistemi.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="3c2c4-510">Sebbene l'esempio di argomento fornisca un esempio funzionante di tecniche di convalida, non `FileHelpers` implementare la classe in un'app di produzione a meno che non si:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="3c2c4-511">Comprendere completamente l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="3c2c4-512">Modificare l'implementazione in modo appropriato per l'ambiente e le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="3c2c4-513">**Non implementare mai indiscriminatamente il codice di sicurezza in un'app senza soddisfare questi requisiti.**</span><span class="sxs-lookup"><span data-stu-id="3c2c4-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="3c2c4-514">Convalida contenuto</span><span class="sxs-lookup"><span data-stu-id="3c2c4-514">Content validation</span></span>

<span data-ttu-id="3c2c4-515">**Usare un'API di analisi di virus/malware di terze parti nel contenuto caricato.**</span><span class="sxs-lookup"><span data-stu-id="3c2c4-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="3c2c4-516">L'analisi dei file richiede le risorse del server in scenari con volumi elevati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="3c2c4-517">Se le prestazioni di elaborazione delle richieste sono diminuite a causa dell'analisi dei file, provare a eseguire l'offload del lavoro di analisi in un [servizio in background](xref:fundamentals/host/hosted-services), possibilmente in un servizio in esecuzione in un server diverso dal server dell'app.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="3c2c4-518">In genere, i file caricati vengono mantenuti in un'area in quarantena finché non vengono controllati dal programma antivirus in background.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="3c2c4-519">Quando un file passa, il file viene spostato nel percorso di archiviazione file normale.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="3c2c4-520">Questi passaggi vengono in genere eseguiti insieme a un record di database che indica lo stato di analisi di un file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="3c2c4-521">Usando un approccio di questo tipo, l'app e il server app rimangono incentrati sulla risposta alle richieste.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="3c2c4-522">Convalida dell'estensione di file</span><span class="sxs-lookup"><span data-stu-id="3c2c4-522">File extension validation</span></span>

<span data-ttu-id="3c2c4-523">L'estensione del file caricato deve essere verificata rispetto a un elenco di estensioni consentite.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="3c2c4-524">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="3c2c4-525">Convalida della firma del file</span><span class="sxs-lookup"><span data-stu-id="3c2c4-525">File signature validation</span></span>

<span data-ttu-id="3c2c4-526">La firma di un file è determinata dai primi byte all'inizio di un file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="3c2c4-527">Questi byte possono essere usati per indicare se l'estensione corrisponde al contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="3c2c4-528">L'app di esempio controlla le firme dei file per alcuni tipi di file comuni.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="3c2c4-529">Nell'esempio seguente, la firma del file per un'immagine JPEG viene verificata rispetto al file:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="3c2c4-530">Per ottenere ulteriori firme di file, vedere il [database delle firme file](https://www.filesignatures.net/) e le specifiche del file ufficiale.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="3c2c4-531">Sicurezza del nome file</span><span class="sxs-lookup"><span data-stu-id="3c2c4-531">File name security</span></span>

<span data-ttu-id="3c2c4-532">Non usare mai un nome file fornito dal client per salvare un file nell'archiviazione fisica.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="3c2c4-533">Creare un nome file sicuro per il file usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per creare un percorso completo (incluso il nome file) per l'archiviazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="3c2c4-534">codifica HTML automaticamente i valori delle proprietà per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-534"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="3c2c4-535">Il codice seguente è sicuro da usare:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="3c2c4-536">Al di Razorfuori di <xref:System.Net.WebUtility.HtmlEncode*> , sempre il contenuto del nome file da una richiesta dell'utente.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="3c2c4-537">Molte implementazioni devono includere un controllo per verificare che il file esista. in caso contrario, il file viene sovrascritto da un file con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="3c2c4-538">Fornire logica aggiuntiva per soddisfare le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="3c2c4-539">Convalida dimensioni</span><span class="sxs-lookup"><span data-stu-id="3c2c4-539">Size validation</span></span>

<span data-ttu-id="3c2c4-540">Limitare le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="3c2c4-541">Nell'app di esempio, le dimensioni del file sono limitate a 2 MB (indicate in byte).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="3c2c4-542">Il limite viene fornito tramite la [configurazione](xref:fundamentals/configuration/index) dal file *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="3c2c4-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="3c2c4-543">`FileSizeLimit` Viene inserito nelle `PageModel` classi:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="3c2c4-544">Quando le dimensioni di un file superano il limite, il file viene rifiutato:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="3c2c4-545">Corrisponde al valore dell'attributo Name al nome del parametro del metodo POST</span><span class="sxs-lookup"><span data-stu-id="3c2c4-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="3c2c4-546">In nonRazor form che inviano dati del modulo o usano `FormData` direttamente il nome di JavaScript, il nome specificato nell'elemento del `FormData` form o deve corrispondere al nome del parametro nell'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="3c2c4-547">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-547">In the following example:</span></span>

* <span data-ttu-id="3c2c4-548">Quando si usa `<input>` un elemento, `name` l'attributo viene impostato sul valore `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="3c2c4-549">Quando si `FormData` USA in JavaScript, il nome viene impostato sul valore `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="3c2c4-550">Usare un nome corrispondente per il parametro del metodo C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="3c2c4-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="3c2c4-551">Per un Razor metodo gestore pagina pagine denominato `Upload`:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="3c2c4-552">Per un metodo di azione POST controller MVC:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="3c2c4-553">Configurazione del server e dell'app</span><span class="sxs-lookup"><span data-stu-id="3c2c4-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="3c2c4-554">Limite di lunghezza del corpo multipart</span><span class="sxs-lookup"><span data-stu-id="3c2c4-554">Multipart body length limit</span></span>

<span data-ttu-id="3c2c4-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>imposta il limite per la lunghezza di ogni corpo multipart.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="3c2c4-556">Le sezioni del modulo che superano questo <xref:System.IO.InvalidDataException> limite generano un'espressione quando vengono analizzate.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="3c2c4-557">Il valore predefinito è 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="3c2c4-558">Personalizzare il limite usando l' <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> impostazione in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="3c2c4-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>viene usato per impostare <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="3c2c4-560">In un' Razor app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="3c2c4-561">In un' Razor app pagine o in un'app MVC, applicare il filtro al modello di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="3c2c4-562">Dimensioni massime del corpo della richiesta di gheppio</span><span class="sxs-lookup"><span data-stu-id="3c2c4-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="3c2c4-563">Per le app ospitate da gheppio, le dimensioni massime predefinite del corpo della richiesta sono pari a 30 milioni byte, ovvero circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="3c2c4-564">Personalizzare il limite usando l'opzione del server [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) gheppio:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="3c2c4-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>viene usato per impostare [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="3c2c4-566">In un' Razor app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="3c2c4-567">In un' Razor app pagine o in un'app MVC, applicare il filtro alla classe del gestore di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="3c2c4-568">Altri limiti del gheppio</span><span class="sxs-lookup"><span data-stu-id="3c2c4-568">Other Kestrel limits</span></span>

<span data-ttu-id="3c2c4-569">Per le app ospitate da gheppio possono essere applicati altri limiti di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="3c2c4-570">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="3c2c4-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="3c2c4-571">Frequenza dei dati di richiesta e risposta</span><span class="sxs-lookup"><span data-stu-id="3c2c4-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="3c2c4-572">Limite lunghezza contenuto IIS</span><span class="sxs-lookup"><span data-stu-id="3c2c4-572">IIS content length limit</span></span>

<span data-ttu-id="3c2c4-573">Il limite di richieste predefinito`maxAllowedContentLength`() è 30 milioni byte, che corrisponde a circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="3c2c4-574">Personalizzare il limite nel file *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="3c2c4-574">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="3c2c4-575">Questa impostazione si applica solo a IIS.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-575">This setting only applies to IIS.</span></span> <span data-ttu-id="3c2c4-576">Il comportamento non si verifica per impostazione predefinita con l'hosting in Kestrel.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="3c2c4-577">Per altre informazioni, vedere [limiti \<delle richieste requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="3c2c4-578">Le limitazioni del modulo ASP.NET Core o della presenza del modulo filtro richieste IIS possono limitare il caricamento a 2 o 4 GB.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="3c2c4-579">Per altre informazioni, vedere [non è possibile caricare file di dimensioni superiori a 2 GB (DotNet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="3c2c4-580">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="3c2c4-580">Troubleshoot</span></span>

<span data-ttu-id="3c2c4-581">Di seguito sono trattati alcuni problemi comuni riscontrati durante il caricamento di file, con le soluzioni possibili corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="3c2c4-582">Errore non trovato quando viene distribuito in un server IIS</span><span class="sxs-lookup"><span data-stu-id="3c2c4-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="3c2c4-583">L'errore seguente indica che il file caricato supera la lunghezza del contenuto configurata del server:</span><span class="sxs-lookup"><span data-stu-id="3c2c4-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="3c2c4-584">Per ulteriori informazioni sull'aumento del limite, vedere la sezione [limite di lunghezza del contenuto IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="3c2c4-585">Errore di connessione</span><span class="sxs-lookup"><span data-stu-id="3c2c4-585">Connection failure</span></span>

<span data-ttu-id="3c2c4-586">Un errore di connessione e una connessione al server di reimpostazione probabilmente indicano che il file caricato supera le dimensioni massime del corpo della richiesta del gheppio.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="3c2c4-587">Per ulteriori informazioni, vedere la sezione relativa alle [dimensioni massime del corpo della richiesta di Gheppio](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="3c2c4-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="3c2c4-588">I limiti di connessione del client gheppio possono richiedere anche modifiche.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="3c2c4-589">Eccezione per riferimento Null con IFormFile</span><span class="sxs-lookup"><span data-stu-id="3c2c4-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="3c2c4-590">Se il <xref:Microsoft.AspNetCore.Http.IFormFile> controller accetta file caricati utilizzando, ma il valore è `null`, verificare che il form HTML specifichi un `enctype` valore di. `multipart/form-data`</span><span class="sxs-lookup"><span data-stu-id="3c2c4-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="3c2c4-591">Se questo attributo non è impostato sull' `<form>` elemento, il caricamento del file non viene eseguito e <xref:Microsoft.AspNetCore.Http.IFormFile> gli argomenti `null`associati sono.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="3c2c4-592">Verificare inoltre che la [denominazione di caricamento nei dati del modulo corrisponda alla denominazione dell'app](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="3c2c4-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="3c2c4-593">Il flusso è troppo lungo</span><span class="sxs-lookup"><span data-stu-id="3c2c4-593">Stream was too long</span></span>

<span data-ttu-id="3c2c4-594">Gli esempi in questo argomento si basano su <xref:System.IO.MemoryStream> per conservare il contenuto del file caricato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="3c2c4-595">Il limite delle dimensioni di `MemoryStream` un `int.MaxValue`oggetto è.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="3c2c4-596">Se lo scenario di caricamento dei file dell'app richiede un contenuto del file di dimensioni superiori a 50 MB, usare un approccio alternativo che `MemoryStream` non si basa su un singolo per contenere il contenuto di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="3c2c4-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="3c2c4-597">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3c2c4-597">Additional resources</span></span>

* [<span data-ttu-id="3c2c4-598">Svuotamento della richiesta di connessione HTTP</span><span class="sxs-lookup"><span data-stu-id="3c2c4-598">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http-connection-request-draining)
* [<span data-ttu-id="3c2c4-599">Caricamento di file senza restrizioni</span><span class="sxs-lookup"><span data-stu-id="3c2c4-599">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="3c2c4-600">Sicurezza di Azure: frame di sicurezza: convalida dell'input | Attenuazioni</span><span class="sxs-lookup"><span data-stu-id="3c2c4-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="3c2c4-601">Modelli di progettazione cloud di Azure: modello di chiave del posteggiatore</span><span class="sxs-lookup"><span data-stu-id="3c2c4-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
