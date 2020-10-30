---
title: Caricare file in ASP.NET Core
author: rick-anderson
description: Come usare l'associazione del modello e lo streaming per caricare i file in ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/models/file-uploads
ms.openlocfilehash: 14561bace565c104d0a9c926cad3105c4865e72a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061171"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="c18ce-103">Caricare file in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c18ce-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="c18ce-104">Di [Steve Smith](https://ardalis.com/) e [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="c18ce-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c18ce-105">ASP.NET Core supporta il caricamento di uno o più file mediante l'associazione di modelli memorizzati nel buffer per i file più piccoli e il flusso non memorizzato nel buffer per file di dimensioni maggiori.</span><span class="sxs-lookup"><span data-stu-id="c18ce-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="c18ce-106">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c18ce-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="c18ce-107">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="c18ce-107">Security considerations</span></span>

<span data-ttu-id="c18ce-108">Prestare attenzione quando si fornisce agli utenti la possibilità di caricare file in un server.</span><span class="sxs-lookup"><span data-stu-id="c18ce-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="c18ce-109">Gli utenti malintenzionati possono tentare di:</span><span class="sxs-lookup"><span data-stu-id="c18ce-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="c18ce-110">Eseguire attacchi [di tipo Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="c18ce-111">Caricare virus o malware.</span><span class="sxs-lookup"><span data-stu-id="c18ce-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="c18ce-112">Compromettere le reti e i server in altri modi.</span><span class="sxs-lookup"><span data-stu-id="c18ce-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="c18ce-113">I passaggi di sicurezza che riducono la probabilità di un attacco riuscito sono:</span><span class="sxs-lookup"><span data-stu-id="c18ce-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="c18ce-114">Caricare i file in un'area di caricamento file dedicata, preferibilmente in un'unità non di sistema.</span><span class="sxs-lookup"><span data-stu-id="c18ce-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="c18ce-115">Un percorso dedicato semplifica l'imposizione delle restrizioni di sicurezza sui file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="c18ce-116">Disabilitare le autorizzazioni di esecuzione per il percorso di caricamento del file.&dagger;</span><span class="sxs-lookup"><span data-stu-id="c18ce-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="c18ce-117">Non **rende permanente i** file caricati nella stessa struttura di directory dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="c18ce-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="c18ce-118">Usare un nome file sicuro determinato dall'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="c18ce-119">Non usare un nome file fornito dall'utente o il nome file non attendibile del file caricato. &dagger; HTML codifica il nome file non attendibile quando viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="c18ce-120">Ad esempio, registrare il nome del file o visualizzarlo nell'interfaccia utente (l' :::no-loc(Razor)::: output viene codificato automaticamente in HTML).</span><span class="sxs-lookup"><span data-stu-id="c18ce-120">For example, logging the file name or displaying in UI (:::no-loc(Razor)::: automatically HTML encodes output).</span></span>
* <span data-ttu-id="c18ce-121">Consenti solo le estensioni di file approvate per la specifica di progettazione dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="c18ce-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="c18ce-122">Verificare che i controlli lato client vengano eseguiti sul server. &dagger; I controlli lato client sono facili da aggirare.</span><span class="sxs-lookup"><span data-stu-id="c18ce-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="c18ce-123">Controllare le dimensioni di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="c18ce-124">Impostare un limite di dimensioni massime per impedire caricamenti di grandi dimensioni.&dagger;</span><span class="sxs-lookup"><span data-stu-id="c18ce-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="c18ce-125">Quando i file non devono essere sovrascritti da un file caricato con lo stesso nome, controllare il nome del file sul database o sull'archiviazione fisica prima di caricare il file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="c18ce-126">**Eseguire uno scanner virus/malware sul contenuto caricato prima che il file venga archiviato.**</span><span class="sxs-lookup"><span data-stu-id="c18ce-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="c18ce-127">&dagger;L'app di esempio illustra un approccio che soddisfa i criteri.</span><span class="sxs-lookup"><span data-stu-id="c18ce-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="c18ce-128">Il caricamento di codice dannoso in un sistema è spesso il primo passaggio per l'esecuzione di codice in grado di:</span><span class="sxs-lookup"><span data-stu-id="c18ce-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="c18ce-129">Ottenere il controllo completo di un sistema.</span><span class="sxs-lookup"><span data-stu-id="c18ce-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="c18ce-130">Sovraccaricare un sistema con il risultato che si verifica un arresto anomalo del sistema.</span><span class="sxs-lookup"><span data-stu-id="c18ce-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="c18ce-131">Compromettere i dati di sistemi o utenti.</span><span class="sxs-lookup"><span data-stu-id="c18ce-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="c18ce-132">Applicare i graffiti a un'interfaccia utente pubblica.</span><span class="sxs-lookup"><span data-stu-id="c18ce-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="c18ce-133">Per informazioni sulla riduzione della superficie di attacco quando si accettano file dagli utenti, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="c18ce-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="c18ce-134">Caricamento di file senza restrizioni</span><span class="sxs-lookup"><span data-stu-id="c18ce-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="c18ce-135">Protezione di Azure: Verificare i controlli appropriati quando si accettano file dagli utenti</span><span class="sxs-lookup"><span data-stu-id="c18ce-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="c18ce-136">Per ulteriori informazioni sull'implementazione di misure di sicurezza, inclusi esempi dall'app di esempio, vedere la sezione [convalida](#validation) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="c18ce-137">Scenari di archiviazione</span><span class="sxs-lookup"><span data-stu-id="c18ce-137">Storage scenarios</span></span>

<span data-ttu-id="c18ce-138">Le opzioni di archiviazione comuni per i file includono:</span><span class="sxs-lookup"><span data-stu-id="c18ce-138">Common storage options for files include:</span></span>

* <span data-ttu-id="c18ce-139">Database</span><span class="sxs-lookup"><span data-stu-id="c18ce-139">Database</span></span>

  * <span data-ttu-id="c18ce-140">Per i caricamenti di file di piccole dimensioni, un database è spesso più veloce rispetto alle opzioni di archiviazione fisica (file system o condivisione di rete).</span><span class="sxs-lookup"><span data-stu-id="c18ce-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="c18ce-141">Un database è spesso più pratico delle opzioni di archiviazione fisica perché il recupero di un record di database per i dati utente può fornire simultaneamente il contenuto del file, ad esempio un'immagine avatar.</span><span class="sxs-lookup"><span data-stu-id="c18ce-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="c18ce-142">Un database è potenzialmente meno costoso rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="c18ce-143">Archiviazione fisica (file system o condivisione di rete)</span><span class="sxs-lookup"><span data-stu-id="c18ce-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="c18ce-144">Per i caricamenti di file di grandi dimensioni:</span><span class="sxs-lookup"><span data-stu-id="c18ce-144">For large file uploads:</span></span>
    * <span data-ttu-id="c18ce-145">I limiti del database possono limitare le dimensioni del caricamento.</span><span class="sxs-lookup"><span data-stu-id="c18ce-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="c18ce-146">L'archiviazione fisica è spesso meno economica rispetto all'archiviazione in un database.</span><span class="sxs-lookup"><span data-stu-id="c18ce-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="c18ce-147">L'archiviazione fisica è potenzialmente meno costosa rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="c18ce-148">Il processo dell'app deve avere le autorizzazioni di lettura e scrittura per il percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="c18ce-149">**Non concedere mai l'autorizzazione Execute.**</span><span class="sxs-lookup"><span data-stu-id="c18ce-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="c18ce-150">Servizio di archiviazione dei dati (ad esempio, [archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="c18ce-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="c18ce-151">I servizi in genere offrono una maggiore scalabilità e resilienza rispetto a soluzioni locali che in genere sono soggette a singoli punti di errore.</span><span class="sxs-lookup"><span data-stu-id="c18ce-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="c18ce-152">I servizi sono un costo potenzialmente inferiore in scenari di infrastruttura di archiviazione di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="c18ce-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="c18ce-153">Per altre informazioni, vedere [Guida introduttiva: usare .NET per creare un BLOB nell'archivio oggetti](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="c18ce-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="c18ce-154">Scenari di caricamento di file</span><span class="sxs-lookup"><span data-stu-id="c18ce-154">File upload scenarios</span></span>

<span data-ttu-id="c18ce-155">Due approcci generali per il caricamento di file sono il buffering e il flusso.</span><span class="sxs-lookup"><span data-stu-id="c18ce-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="c18ce-156">**responseBuffering**</span><span class="sxs-lookup"><span data-stu-id="c18ce-156">**Buffering**</span></span>

<span data-ttu-id="c18ce-157">L'intero file viene letto in un oggetto <xref:Microsoft.AspNetCore.Http.IFormFile> , ovvero una rappresentazione C# del file usato per elaborare o salvare il file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="c18ce-158">Le risorse (disco, memoria) utilizzate dai caricamenti di file dipendono dal numero e dalle dimensioni dei caricamenti di file simultanei.</span><span class="sxs-lookup"><span data-stu-id="c18ce-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="c18ce-159">Se un'app tenta di memorizzare nel buffer un numero eccessivo di caricamenti, il sito si arresta in modo anomalo quando esaurisce la memoria o lo spazio su disco.</span><span class="sxs-lookup"><span data-stu-id="c18ce-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="c18ce-160">Se le dimensioni o la frequenza dei caricamenti di file esauriscono le risorse dell'app, usare il flusso.</span><span class="sxs-lookup"><span data-stu-id="c18ce-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="c18ce-161">Ogni singolo file memorizzato nel buffer che supera 64 KB viene spostato dalla memoria in un file temporaneo su disco.</span><span class="sxs-lookup"><span data-stu-id="c18ce-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="c18ce-162">Il buffering di file di piccole dimensioni viene trattato nelle sezioni seguenti di questo argomento:</span><span class="sxs-lookup"><span data-stu-id="c18ce-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="c18ce-163">Archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="c18ce-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="c18ce-164">Database</span><span class="sxs-lookup"><span data-stu-id="c18ce-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="c18ce-165">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="c18ce-165">**Streaming**</span></span>

<span data-ttu-id="c18ce-166">Il file viene ricevuto da una richiesta multipart ed elaborato o salvato direttamente dall'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="c18ce-167">Il flusso non migliora significativamente le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="c18ce-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="c18ce-168">Il flusso riduce le richieste di memoria o di spazio su disco durante il caricamento dei file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="c18ce-169">Il flusso di file di grandi dimensioni è trattato nella sezione [caricare file di grandi dimensioni con flusso](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="c18ce-170">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer all'archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="c18ce-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="c18ce-171">Per caricare file di piccole dimensioni, usare un modulo multipart o creare una richiesta POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c18ce-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="c18ce-172">L'esempio seguente illustra l'uso di un :::no-loc(Razor)::: form di pagine per caricare un singolo file ( *pages/BufferedSingleFileUploadPhysical. cshtml* nell'app di esempio):</span><span class="sxs-lookup"><span data-stu-id="c18ce-172">The following example demonstrates the use of a :::no-loc(Razor)::: Pages form to upload a single file ( *Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="c18ce-173">L'esempio seguente è analogo all'esempio precedente, ad eccezione del fatto che:</span><span class="sxs-lookup"><span data-stu-id="c18ce-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="c18ce-174">Per inviare i dati del modulo, viene usato JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="c18ce-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="c18ce-175">Nessuna convalida.</span><span class="sxs-lookup"><span data-stu-id="c18ce-175">There's no validation.</span></span>

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

<span data-ttu-id="c18ce-176">Per eseguire il POST del form in JavaScript per i client che [non supportano l'API fetch](https://caniuse.com/#feat=fetch), usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="c18ce-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="c18ce-177">Usare un riempimento di recupero (ad esempio, [Window. fetch Refill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="c18ce-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="c18ce-178">Usare `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="c18ce-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="c18ce-179">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-179">For example:</span></span>

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

<span data-ttu-id="c18ce-180">Per supportare il caricamento di file, i moduli HTML devono specificare un tipo di codifica ( `enctype` ) di `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="c18ce-181">Affinché un `files` elemento input supporti il caricamento di più file, fornire l' `multiple` attributo nell' `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="c18ce-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="c18ce-182">È possibile accedere ai singoli file caricati nel server tramite l' [associazione di modelli](xref:mvc/models/model-binding) tramite <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="c18ce-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="c18ce-183">L'app di esempio illustra più caricamenti di file memorizzati nel buffer per scenari di archiviazione di database e fisici.</span><span class="sxs-lookup"><span data-stu-id="c18ce-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="c18ce-184">Non **utilizzare la** `FileName` proprietà di <xref:Microsoft.AspNetCore.Http.IFormFile> diverso da per la visualizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="c18ce-185">Durante la visualizzazione o la registrazione, HTML codifica il nome del file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="c18ce-186">Un utente malintenzionato può fornire un nome file dannoso, inclusi percorsi completi o percorsi relativi.</span><span class="sxs-lookup"><span data-stu-id="c18ce-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="c18ce-187">Le applicazioni devono:</span><span class="sxs-lookup"><span data-stu-id="c18ce-187">Applications should:</span></span>
>
> * <span data-ttu-id="c18ce-188">Rimuovere il percorso dal nome file specificato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="c18ce-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="c18ce-189">Salvare il nome file con codifica HTML, percorso-rimosso per l'interfaccia utente o la registrazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="c18ce-190">Genera un nuovo nome file casuale per l'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="c18ce-191">Il codice seguente rimuove il percorso dal nome del file:</span><span class="sxs-lookup"><span data-stu-id="c18ce-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="c18ce-192">Gli esempi forniti finora non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="c18ce-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="c18ce-193">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="c18ce-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="c18ce-194">Considerazioni sulla sicurezza</span><span class="sxs-lookup"><span data-stu-id="c18ce-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="c18ce-195">Convalida</span><span class="sxs-lookup"><span data-stu-id="c18ce-195">Validation</span></span>](#validation)

<span data-ttu-id="c18ce-196">Quando si caricano file usando l'associazione di modelli e <xref:Microsoft.AspNetCore.Http.IFormFile> , il metodo di azione può accettare:</span><span class="sxs-lookup"><span data-stu-id="c18ce-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="c18ce-197">Singolo oggetto <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="c18ce-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="c18ce-198">Qualsiasi raccolta seguente che rappresenta diversi file:</span><span class="sxs-lookup"><span data-stu-id="c18ce-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="c18ce-199">[Elenco](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="c18ce-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="c18ce-200">L'associazione corrisponde ai file di form in base al nome.</span><span class="sxs-lookup"><span data-stu-id="c18ce-200">Binding matches form files by name.</span></span> <span data-ttu-id="c18ce-201">Ad esempio, il `name` valore HTML in `<input type="file" name="formFile">` deve corrispondere al parametro/proprietà di C# associato ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="c18ce-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="c18ce-202">Per ulteriori informazioni, vedere la sezione [corrispondenza del nome dell'attributo del nome del parametro del metodo post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="c18ce-203">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="c18ce-203">The following example:</span></span>

* <span data-ttu-id="c18ce-204">Esegue il ciclo di uno o più file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="c18ce-205">USA [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per restituire un percorso completo per un file, incluso il nome del file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="c18ce-206">Salva i file nella file system locale usando un nome file generato dall'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="c18ce-207">Restituisce il numero totale e le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="c18ce-208">Utilizzare `Path.GetRandomFileName` per generare un nome file senza percorso.</span><span class="sxs-lookup"><span data-stu-id="c18ce-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="c18ce-209">Nell'esempio seguente il percorso viene ottenuto dalla configurazione:</span><span class="sxs-lookup"><span data-stu-id="c18ce-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="c18ce-210">Il percorso passato a <xref:System.IO.FileStream> *deve* includere il nome del file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="c18ce-211">Se il nome file non viene specificato, <xref:System.UnauthorizedAccessException> viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="c18ce-212">I file caricati con la <xref:Microsoft.AspNetCore.Http.IFormFile> tecnica vengono memorizzati nel buffer in memoria o su disco sul server prima dell'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="c18ce-213">All'interno del metodo di azione, il <xref:Microsoft.AspNetCore.Http.IFormFile> contenuto è accessibile come <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="c18ce-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="c18ce-214">Oltre al file system locale, i file possono essere salvati in una condivisione di rete o in un servizio di archiviazione file, ad esempio [archiviazione BLOB di Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="c18ce-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="c18ce-215">Per un altro esempio che esegue il ciclo su più file per il caricamento e usa nomi di file sicuri, vedere *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="c18ce-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="c18ce-216">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) genera un'eccezione <xref:System.IO.IOException> se vengono creati più di 65.535 file senza eliminare i file temporanei precedenti.</span><span class="sxs-lookup"><span data-stu-id="c18ce-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="c18ce-217">Il limite di 65.535 file è un limite per server.</span><span class="sxs-lookup"><span data-stu-id="c18ce-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="c18ce-218">Per ulteriori informazioni su questo limite per il sistema operativo Windows, vedere la sezione Osservazioni negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="c18ce-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="c18ce-219">GetTempFileNameA (funzione)</span><span class="sxs-lookup"><span data-stu-id="c18ce-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="c18ce-220">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer in un database</span><span class="sxs-lookup"><span data-stu-id="c18ce-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="c18ce-221">Per archiviare i dati dei file binari in un database utilizzando [Entity Framework](/ef/core/index), definire una <xref:System.Byte> proprietà di matrice nell'entità:</span><span class="sxs-lookup"><span data-stu-id="c18ce-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="c18ce-222">Specificare una proprietà del modello di pagina per la classe che include <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="c18ce-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="c18ce-223"><xref:Microsoft.AspNetCore.Http.IFormFile> può essere usato direttamente come parametro del metodo di azione o come proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="c18ce-224">Nell'esempio precedente viene utilizzata una proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="c18ce-225">Il `FileUpload` viene utilizzato nel :::no-loc(Razor)::: form pagine:</span><span class="sxs-lookup"><span data-stu-id="c18ce-225">The `FileUpload` is used in the :::no-loc(Razor)::: Pages form:</span></span>

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

<span data-ttu-id="c18ce-226">Quando il modulo viene inviato al server, copiarlo in <xref:Microsoft.AspNetCore.Http.IFormFile> un flusso e salvarlo come una matrice di byte nel database.</span><span class="sxs-lookup"><span data-stu-id="c18ce-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="c18ce-227">Nell'esempio seguente, `_dbContext` archivia il contesto di database dell'app:</span><span class="sxs-lookup"><span data-stu-id="c18ce-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="c18ce-228">L'esempio precedente è simile a uno scenario illustrato nell'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="c18ce-229">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c18ce-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="c18ce-230">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="c18ce-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="c18ce-231">Quando si archiviano dati binari all'interno di database relazionali, è necessario prestare attenzione, perché l'operazione può influire negativamente sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="c18ce-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="c18ce-232">Non fare affidamento o considerare attendibile la `FileName` proprietà di <xref:Microsoft.AspNetCore.Http.IFormFile> senza convalida.</span><span class="sxs-lookup"><span data-stu-id="c18ce-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="c18ce-233">La `FileName` proprietà deve essere usata solo a scopo di visualizzazione e solo dopo la codifica HTML.</span><span class="sxs-lookup"><span data-stu-id="c18ce-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="c18ce-234">Gli esempi forniti non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="c18ce-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="c18ce-235">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="c18ce-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="c18ce-236">Considerazioni sulla sicurezza</span><span class="sxs-lookup"><span data-stu-id="c18ce-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="c18ce-237">Convalida</span><span class="sxs-lookup"><span data-stu-id="c18ce-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="c18ce-238">Caricare file di grandi dimensioni con flusso</span><span class="sxs-lookup"><span data-stu-id="c18ce-238">Upload large files with streaming</span></span>

<span data-ttu-id="c18ce-239">Nell'esempio seguente viene illustrato come utilizzare JavaScript per trasmettere un file a un'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="c18ce-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="c18ce-240">Il token antifalsificazione del file viene generato usando un attributo di filtro personalizzato e passato alle intestazioni HTTP del client anziché nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="c18ce-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="c18ce-241">Poiché il metodo di azione elabora direttamente i dati caricati, l'associazione del modello di modulo viene disabilitata da un altro filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="c18ce-242">All'interno dell'azione, il contenuto del form viene letto tramite un `MultipartReader`, che legge ogni singola `MultipartSection`, elaborando il file o archiviandone il contenuto, come appropriato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="c18ce-243">Una volta lette le sezioni multiparte, l'azione esegue una propria associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c18ce-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="c18ce-244">La risposta della pagina iniziale carica il form e salva un token antifalsificazione in un :::no-loc(cookie)::: (tramite l' `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` attributo).</span><span class="sxs-lookup"><span data-stu-id="c18ce-244">The initial page response loads the form and saves an antiforgery token in a :::no-loc(cookie)::: (via the `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` attribute).</span></span> <span data-ttu-id="c18ce-245">L'attributo usa il [supporto antifalsificazione](xref:security/anti-request-forgery) predefinito di ASP.NET Core per impostare un :::no-loc(cookie)::: con un token di richiesta:</span><span class="sxs-lookup"><span data-stu-id="c18ce-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a :::no-loc(cookie)::: with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute)]

<span data-ttu-id="c18ce-246">`DisableFormValueModelBindingAttribute`Viene usato per disabilitare l'associazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="c18ce-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="c18ce-247">Nell'app di esempio `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` e `DisableFormValueModelBindingAttribute` vengono applicati come filtri ai modelli di applicazione della pagina di `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` all' `Startup.ConfigureServices` uso delle [ :::no-loc(Razor)::: convenzioni delle pagine](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="c18ce-247">In the sample app, `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [:::no-loc(Razor)::: Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_Add:::no-loc(Razor):::Pages&highlight=7-10,16-19)]

<span data-ttu-id="c18ce-248">Poiché l'associazione di modelli non legge il modulo, i parametri associati al modulo non vengono associati (la query, la route e l'intestazione continuano a funzionare).</span><span class="sxs-lookup"><span data-stu-id="c18ce-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="c18ce-249">Il metodo di azione funziona direttamente con la `Request` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="c18ce-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="c18ce-250">Per leggere ogni sezione, viene usato un `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="c18ce-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="c18ce-251">I dati chiave/valore vengono archiviati in un oggetto `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="c18ce-252">Una volta lette le sezioni multipart, il contenuto di `KeyValueAccumulator` viene utilizzato per associare i dati del modulo a un tipo di modello.</span><span class="sxs-lookup"><span data-stu-id="c18ce-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="c18ce-253">Metodo completo `StreamingController.UploadDatabase` per lo streaming in un database con EF Core:</span><span class="sxs-lookup"><span data-stu-id="c18ce-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="c18ce-254">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper. cs* ):</span><span class="sxs-lookup"><span data-stu-id="c18ce-254">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="c18ce-255">Il `StreamingController.UploadPhysical` metodo completo per lo streaming in una posizione fisica:</span><span class="sxs-lookup"><span data-stu-id="c18ce-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="c18ce-256">Nell'app di esempio, i controlli di convalida vengono gestiti da `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="c18ce-257">Convalida</span><span class="sxs-lookup"><span data-stu-id="c18ce-257">Validation</span></span>

<span data-ttu-id="c18ce-258">La classe dell'app di esempio `FileHelpers` illustra alcuni controlli per i caricamenti di file memorizzati nel buffer <xref:Microsoft.AspNetCore.Http.IFormFile> e in flusso.</span><span class="sxs-lookup"><span data-stu-id="c18ce-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="c18ce-259">Per l'elaborazione dei <xref:Microsoft.AspNetCore.Http.IFormFile> caricamenti di file memorizzati nel buffer nell'app di esempio, vedere il `ProcessFormFile` metodo nel file *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="c18ce-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="c18ce-260">Per l'elaborazione di file trasmessi, vedere il `ProcessStreamedFile` Metodo nello stesso file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="c18ce-261">I metodi di elaborazione della convalida illustrati nell'app di esempio non analizzano il contenuto dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="c18ce-262">Nella maggior parte degli scenari di produzione, nel file viene usata un'API scanner di virus/malware prima di rendere disponibile il file per gli utenti o altri sistemi.</span><span class="sxs-lookup"><span data-stu-id="c18ce-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="c18ce-263">Sebbene l'esempio di argomento fornisca un esempio funzionante di tecniche di convalida, non implementare la `FileHelpers` classe in un'app di produzione a meno che non si:</span><span class="sxs-lookup"><span data-stu-id="c18ce-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="c18ce-264">Comprendere completamente l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="c18ce-265">Modificare l'implementazione in modo appropriato per l'ambiente e le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="c18ce-266">**Non implementare mai indiscriminatamente il codice di sicurezza in un'app senza soddisfare questi requisiti.**</span><span class="sxs-lookup"><span data-stu-id="c18ce-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="c18ce-267">Convalida contenuto</span><span class="sxs-lookup"><span data-stu-id="c18ce-267">Content validation</span></span>

<span data-ttu-id="c18ce-268">**Usare un'API di analisi di virus/malware di terze parti nel contenuto caricato.**</span><span class="sxs-lookup"><span data-stu-id="c18ce-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="c18ce-269">L'analisi dei file richiede le risorse del server in scenari con volumi elevati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="c18ce-270">Se le prestazioni di elaborazione delle richieste sono diminuite a causa dell'analisi dei file, provare a eseguire l'offload del lavoro di analisi in un [servizio in background](xref:fundamentals/host/hosted-services), possibilmente in un servizio in esecuzione in un server diverso dal server dell'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="c18ce-271">In genere, i file caricati vengono mantenuti in un'area in quarantena finché non vengono controllati dal programma antivirus in background.</span><span class="sxs-lookup"><span data-stu-id="c18ce-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="c18ce-272">Quando un file passa, il file viene spostato nel percorso di archiviazione file normale.</span><span class="sxs-lookup"><span data-stu-id="c18ce-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="c18ce-273">Questi passaggi vengono in genere eseguiti insieme a un record di database che indica lo stato di analisi di un file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="c18ce-274">Usando un approccio di questo tipo, l'app e il server app rimangono incentrati sulla risposta alle richieste.</span><span class="sxs-lookup"><span data-stu-id="c18ce-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="c18ce-275">Convalida dell'estensione di file</span><span class="sxs-lookup"><span data-stu-id="c18ce-275">File extension validation</span></span>

<span data-ttu-id="c18ce-276">L'estensione del file caricato deve essere verificata rispetto a un elenco di estensioni consentite.</span><span class="sxs-lookup"><span data-stu-id="c18ce-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="c18ce-277">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="c18ce-278">Convalida della firma del file</span><span class="sxs-lookup"><span data-stu-id="c18ce-278">File signature validation</span></span>

<span data-ttu-id="c18ce-279">La firma di un file è determinata dai primi byte all'inizio di un file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="c18ce-280">Questi byte possono essere usati per indicare se l'estensione corrisponde al contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="c18ce-281">L'app di esempio controlla le firme dei file per alcuni tipi di file comuni.</span><span class="sxs-lookup"><span data-stu-id="c18ce-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="c18ce-282">Nell'esempio seguente, la firma del file per un'immagine JPEG viene verificata rispetto al file:</span><span class="sxs-lookup"><span data-stu-id="c18ce-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="c18ce-283">Per ottenere ulteriori firme di file, vedere il [database delle firme file](https://www.filesignatures.net/) e le specifiche del file ufficiale.</span><span class="sxs-lookup"><span data-stu-id="c18ce-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="c18ce-284">Sicurezza del nome file</span><span class="sxs-lookup"><span data-stu-id="c18ce-284">File name security</span></span>

<span data-ttu-id="c18ce-285">Non usare mai un nome file fornito dal client per salvare un file nell'archiviazione fisica.</span><span class="sxs-lookup"><span data-stu-id="c18ce-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="c18ce-286">Creare un nome file sicuro per il file usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per creare un percorso completo (incluso il nome file) per l'archiviazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="c18ce-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="c18ce-287">:::no-loc(Razor)::: codifica HTML automaticamente i valori delle proprietà per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-287">:::no-loc(Razor)::: automatically HTML encodes property values for display.</span></span> <span data-ttu-id="c18ce-288">Il codice seguente è sicuro da usare:</span><span class="sxs-lookup"><span data-stu-id="c18ce-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="c18ce-289">Al di fuori di :::no-loc(Razor)::: , sempre il <xref:System.Net.WebUtility.HtmlEncode*> contenuto del nome file da una richiesta dell'utente.</span><span class="sxs-lookup"><span data-stu-id="c18ce-289">Outside of :::no-loc(Razor):::, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="c18ce-290">Molte implementazioni devono includere un controllo per verificare che il file esista. in caso contrario, il file viene sovrascritto da un file con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="c18ce-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="c18ce-291">Fornire logica aggiuntiva per soddisfare le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="c18ce-292">Convalida dimensioni</span><span class="sxs-lookup"><span data-stu-id="c18ce-292">Size validation</span></span>

<span data-ttu-id="c18ce-293">Limitare le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="c18ce-294">Nell'app di esempio, le dimensioni del file sono limitate a 2 MB (indicate in byte).</span><span class="sxs-lookup"><span data-stu-id="c18ce-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="c18ce-295">Il limite viene fornito tramite la [configurazione](xref:fundamentals/configuration/index) dal *:::no-loc(appsettings.json):::* file:</span><span class="sxs-lookup"><span data-stu-id="c18ce-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *:::no-loc(appsettings.json):::* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="c18ce-296">`FileSizeLimit`Viene inserito nelle `PageModel` classi:</span><span class="sxs-lookup"><span data-stu-id="c18ce-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="c18ce-297">Quando le dimensioni di un file superano il limite, il file viene rifiutato:</span><span class="sxs-lookup"><span data-stu-id="c18ce-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="c18ce-298">Corrisponde al valore dell'attributo Name al nome del parametro del metodo POST</span><span class="sxs-lookup"><span data-stu-id="c18ce-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="c18ce-299">In non :::no-loc(Razor)::: form che inviano dati del modulo o usano direttamente il nome di JavaScript `FormData` , il nome specificato nell'elemento del form o `FormData` deve corrispondere al nome del parametro nell'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="c18ce-299">In non-:::no-loc(Razor)::: forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="c18ce-300">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="c18ce-300">In the following example:</span></span>

* <span data-ttu-id="c18ce-301">Quando si usa un `<input>` elemento, l' `name` attributo viene impostato sul valore `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="c18ce-302">Quando `FormData` si usa in JavaScript, il nome viene impostato sul valore `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="c18ce-303">Usare un nome corrispondente per il parametro del metodo C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="c18ce-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="c18ce-304">Per un :::no-loc(Razor)::: metodo gestore pagina pagine denominato `Upload` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-304">For a :::no-loc(Razor)::: Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="c18ce-305">Per un metodo di azione POST controller MVC:</span><span class="sxs-lookup"><span data-stu-id="c18ce-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="c18ce-306">Configurazione del server e dell'app</span><span class="sxs-lookup"><span data-stu-id="c18ce-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="c18ce-307">Limite di lunghezza del corpo multipart</span><span class="sxs-lookup"><span data-stu-id="c18ce-307">Multipart body length limit</span></span>

<span data-ttu-id="c18ce-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> imposta il limite per la lunghezza di ogni corpo multipart.</span><span class="sxs-lookup"><span data-stu-id="c18ce-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="c18ce-309">Le sezioni del modulo che superano questo limite generano un'espressione <xref:System.IO.InvalidDataException> quando vengono analizzate.</span><span class="sxs-lookup"><span data-stu-id="c18ce-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="c18ce-310">Il valore predefinito è 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="c18ce-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="c18ce-311">Personalizzare il limite usando l' <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> impostazione in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="c18ce-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> viene usato per impostare <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="c18ce-313">In un' :::no-loc(Razor)::: app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-313">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(Razor):::Pages(options =>
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

<span data-ttu-id="c18ce-314">In un' :::no-loc(Razor)::: app pagine o in un'app MVC, applicare il filtro al modello di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="c18ce-314">In a :::no-loc(Razor)::: Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="c18ce-315">Dimensioni massime del corpo della richiesta di gheppio</span><span class="sxs-lookup"><span data-stu-id="c18ce-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="c18ce-316">Per le app ospitate da gheppio, le dimensioni massime predefinite del corpo della richiesta sono pari a 30 milioni byte, ovvero circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="c18ce-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="c18ce-317">Personalizzare il limite usando l'opzione del server [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) gheppio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="c18ce-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> viene usato per impostare [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="c18ce-319">In un' :::no-loc(Razor)::: app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-319">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(Razor):::Pages(options =>
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

<span data-ttu-id="c18ce-320">In un' :::no-loc(Razor)::: app pagine o in un'app MVC, applicare il filtro alla classe del gestore di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="c18ce-320">In a :::no-loc(Razor)::: pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="c18ce-321">Il `RequestSizeLimitAttribute` può essere applicato anche usando la [`@attribute`](xref:mvc/views/razor#attribute) :::no-loc(Razor)::: direttiva:</span><span class="sxs-lookup"><span data-stu-id="c18ce-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) :::no-loc(Razor)::: directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="c18ce-322">Altri limiti del gheppio</span><span class="sxs-lookup"><span data-stu-id="c18ce-322">Other Kestrel limits</span></span>

<span data-ttu-id="c18ce-323">Per le app ospitate da gheppio possono essere applicati altri limiti di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="c18ce-324">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="c18ce-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="c18ce-325">Frequenza dei dati di richiesta e risposta</span><span class="sxs-lookup"><span data-stu-id="c18ce-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="c18ce-326">IIS</span><span class="sxs-lookup"><span data-stu-id="c18ce-326">IIS</span></span>

<span data-ttu-id="c18ce-327">Il limite di richieste predefinito ( `maxAllowedContentLength` ) è 30 milioni byte, che corrisponde a circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="c18ce-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="c18ce-328">Personalizzare il limite nel `web.config` file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-328">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="c18ce-329">Nell'esempio seguente il limite è impostato su 50 MB (52.428.800 byte):</span><span class="sxs-lookup"><span data-stu-id="c18ce-329">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="c18ce-330">L' `maxAllowedContentLength` impostazione si applica solo a IIS.</span><span class="sxs-lookup"><span data-stu-id="c18ce-330">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="c18ce-331">Per altre informazioni, vedere [limiti `<requestLimits>` della richiesta ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="c18ce-331">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="c18ce-332">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="c18ce-332">Troubleshoot</span></span>

<span data-ttu-id="c18ce-333">Di seguito sono trattati alcuni problemi comuni riscontrati durante il caricamento di file, con le soluzioni possibili corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="c18ce-333">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="c18ce-334">Errore non trovato quando viene distribuito in un server IIS</span><span class="sxs-lookup"><span data-stu-id="c18ce-334">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="c18ce-335">L'errore seguente indica che il file caricato supera la lunghezza del contenuto configurata del server:</span><span class="sxs-lookup"><span data-stu-id="c18ce-335">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="c18ce-336">Per ulteriori informazioni, vedere la sezione [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-336">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="c18ce-337">Errore di connessione</span><span class="sxs-lookup"><span data-stu-id="c18ce-337">Connection failure</span></span>

<span data-ttu-id="c18ce-338">Un errore di connessione e una connessione al server di reimpostazione probabilmente indicano che il file caricato supera le dimensioni massime del corpo della richiesta del gheppio.</span><span class="sxs-lookup"><span data-stu-id="c18ce-338">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="c18ce-339">Per ulteriori informazioni, vedere la sezione relativa alle [dimensioni massime del corpo della richiesta di Gheppio](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-339">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="c18ce-340">I limiti di connessione del client gheppio possono richiedere anche modifiche.</span><span class="sxs-lookup"><span data-stu-id="c18ce-340">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="c18ce-341">Eccezione per riferimento Null con IFormFile</span><span class="sxs-lookup"><span data-stu-id="c18ce-341">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="c18ce-342">Se il controller accetta file caricati utilizzando <xref:Microsoft.AspNetCore.Http.IFormFile> , ma il valore è `null` , verificare che il form HTML specifichi un `enctype` valore di `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-342">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="c18ce-343">Se questo attributo non è impostato sull' `<form>` elemento, il caricamento del file non viene eseguito e gli <xref:Microsoft.AspNetCore.Http.IFormFile> argomenti associati sono `null` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-343">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="c18ce-344">Verificare inoltre che la [denominazione di caricamento nei dati del modulo corrisponda alla denominazione dell'app](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="c18ce-344">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="c18ce-345">Il flusso è troppo lungo</span><span class="sxs-lookup"><span data-stu-id="c18ce-345">Stream was too long</span></span>

<span data-ttu-id="c18ce-346">Gli esempi in questo argomento si basano su <xref:System.IO.MemoryStream> per conservare il contenuto del file caricato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-346">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="c18ce-347">Il limite delle dimensioni di un oggetto `MemoryStream` è `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-347">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="c18ce-348">Se lo scenario di caricamento dei file dell'app richiede un contenuto del file di dimensioni superiori a 50 MB, usare un approccio alternativo che non si basa su un singolo `MemoryStream` per contenere il contenuto di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-348">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="c18ce-349">ASP.NET Core supporta il caricamento di uno o più file mediante l'associazione di modelli memorizzati nel buffer per i file più piccoli e il flusso non memorizzato nel buffer per file di dimensioni maggiori.</span><span class="sxs-lookup"><span data-stu-id="c18ce-349">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="c18ce-350">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c18ce-350">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="c18ce-351">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="c18ce-351">Security considerations</span></span>

<span data-ttu-id="c18ce-352">Prestare attenzione quando si fornisce agli utenti la possibilità di caricare file in un server.</span><span class="sxs-lookup"><span data-stu-id="c18ce-352">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="c18ce-353">Gli utenti malintenzionati possono tentare di:</span><span class="sxs-lookup"><span data-stu-id="c18ce-353">Attackers may attempt to:</span></span>

* <span data-ttu-id="c18ce-354">Eseguire attacchi [di tipo Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-354">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="c18ce-355">Caricare virus o malware.</span><span class="sxs-lookup"><span data-stu-id="c18ce-355">Upload viruses or malware.</span></span>
* <span data-ttu-id="c18ce-356">Compromettere le reti e i server in altri modi.</span><span class="sxs-lookup"><span data-stu-id="c18ce-356">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="c18ce-357">I passaggi di sicurezza che riducono la probabilità di un attacco riuscito sono:</span><span class="sxs-lookup"><span data-stu-id="c18ce-357">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="c18ce-358">Caricare i file in un'area di caricamento file dedicata, preferibilmente in un'unità non di sistema.</span><span class="sxs-lookup"><span data-stu-id="c18ce-358">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="c18ce-359">Un percorso dedicato semplifica l'imposizione delle restrizioni di sicurezza sui file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-359">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="c18ce-360">Disabilitare le autorizzazioni di esecuzione per il percorso di caricamento del file.&dagger;</span><span class="sxs-lookup"><span data-stu-id="c18ce-360">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="c18ce-361">Non **rende permanente i** file caricati nella stessa struttura di directory dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="c18ce-361">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="c18ce-362">Usare un nome file sicuro determinato dall'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-362">Use a safe file name determined by the app.</span></span> <span data-ttu-id="c18ce-363">Non usare un nome file fornito dall'utente o il nome file non attendibile del file caricato. &dagger; HTML codifica il nome file non attendibile quando viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-363">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="c18ce-364">Ad esempio, registrare il nome del file o visualizzarlo nell'interfaccia utente (l' :::no-loc(Razor)::: output viene codificato automaticamente in HTML).</span><span class="sxs-lookup"><span data-stu-id="c18ce-364">For example, logging the file name or displaying in UI (:::no-loc(Razor)::: automatically HTML encodes output).</span></span>
* <span data-ttu-id="c18ce-365">Consenti solo le estensioni di file approvate per la specifica di progettazione dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="c18ce-365">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="c18ce-366">Verificare che i controlli lato client vengano eseguiti sul server. &dagger; I controlli lato client sono facili da aggirare.</span><span class="sxs-lookup"><span data-stu-id="c18ce-366">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="c18ce-367">Controllare le dimensioni di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-367">Check the size of an uploaded file.</span></span> <span data-ttu-id="c18ce-368">Impostare un limite di dimensioni massime per impedire caricamenti di grandi dimensioni.&dagger;</span><span class="sxs-lookup"><span data-stu-id="c18ce-368">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="c18ce-369">Quando i file non devono essere sovrascritti da un file caricato con lo stesso nome, controllare il nome del file sul database o sull'archiviazione fisica prima di caricare il file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-369">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="c18ce-370">**Eseguire uno scanner virus/malware sul contenuto caricato prima che il file venga archiviato.**</span><span class="sxs-lookup"><span data-stu-id="c18ce-370">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="c18ce-371">&dagger;L'app di esempio illustra un approccio che soddisfa i criteri.</span><span class="sxs-lookup"><span data-stu-id="c18ce-371">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="c18ce-372">Il caricamento di codice dannoso in un sistema è spesso il primo passaggio per l'esecuzione di codice in grado di:</span><span class="sxs-lookup"><span data-stu-id="c18ce-372">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="c18ce-373">Ottenere il controllo completo di un sistema.</span><span class="sxs-lookup"><span data-stu-id="c18ce-373">Completely gain control of a system.</span></span>
> * <span data-ttu-id="c18ce-374">Sovraccaricare un sistema con il risultato che si verifica un arresto anomalo del sistema.</span><span class="sxs-lookup"><span data-stu-id="c18ce-374">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="c18ce-375">Compromettere i dati di sistemi o utenti.</span><span class="sxs-lookup"><span data-stu-id="c18ce-375">Compromise user or system data.</span></span>
> * <span data-ttu-id="c18ce-376">Applicare i graffiti a un'interfaccia utente pubblica.</span><span class="sxs-lookup"><span data-stu-id="c18ce-376">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="c18ce-377">Per informazioni sulla riduzione della superficie di attacco quando si accettano file dagli utenti, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="c18ce-377">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="c18ce-378">Caricamento di file senza restrizioni</span><span class="sxs-lookup"><span data-stu-id="c18ce-378">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="c18ce-379">Protezione di Azure: Verificare i controlli appropriati quando si accettano file dagli utenti</span><span class="sxs-lookup"><span data-stu-id="c18ce-379">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="c18ce-380">Per ulteriori informazioni sull'implementazione di misure di sicurezza, inclusi esempi dall'app di esempio, vedere la sezione [convalida](#validation) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-380">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="c18ce-381">Scenari di archiviazione</span><span class="sxs-lookup"><span data-stu-id="c18ce-381">Storage scenarios</span></span>

<span data-ttu-id="c18ce-382">Le opzioni di archiviazione comuni per i file includono:</span><span class="sxs-lookup"><span data-stu-id="c18ce-382">Common storage options for files include:</span></span>

* <span data-ttu-id="c18ce-383">Database</span><span class="sxs-lookup"><span data-stu-id="c18ce-383">Database</span></span>

  * <span data-ttu-id="c18ce-384">Per i caricamenti di file di piccole dimensioni, un database è spesso più veloce rispetto alle opzioni di archiviazione fisica (file system o condivisione di rete).</span><span class="sxs-lookup"><span data-stu-id="c18ce-384">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="c18ce-385">Un database è spesso più pratico delle opzioni di archiviazione fisica perché il recupero di un record di database per i dati utente può fornire simultaneamente il contenuto del file, ad esempio un'immagine avatar.</span><span class="sxs-lookup"><span data-stu-id="c18ce-385">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="c18ce-386">Un database è potenzialmente meno costoso rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-386">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="c18ce-387">Archiviazione fisica (file system o condivisione di rete)</span><span class="sxs-lookup"><span data-stu-id="c18ce-387">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="c18ce-388">Per i caricamenti di file di grandi dimensioni:</span><span class="sxs-lookup"><span data-stu-id="c18ce-388">For large file uploads:</span></span>
    * <span data-ttu-id="c18ce-389">I limiti del database possono limitare le dimensioni del caricamento.</span><span class="sxs-lookup"><span data-stu-id="c18ce-389">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="c18ce-390">L'archiviazione fisica è spesso meno economica rispetto all'archiviazione in un database.</span><span class="sxs-lookup"><span data-stu-id="c18ce-390">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="c18ce-391">L'archiviazione fisica è potenzialmente meno costosa rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-391">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="c18ce-392">Il processo dell'app deve avere le autorizzazioni di lettura e scrittura per il percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-392">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="c18ce-393">**Non concedere mai l'autorizzazione Execute.**</span><span class="sxs-lookup"><span data-stu-id="c18ce-393">**Never grant execute permission.**</span></span>

* <span data-ttu-id="c18ce-394">Servizio di archiviazione dei dati (ad esempio, [archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="c18ce-394">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="c18ce-395">I servizi in genere offrono una maggiore scalabilità e resilienza rispetto a soluzioni locali che in genere sono soggette a singoli punti di errore.</span><span class="sxs-lookup"><span data-stu-id="c18ce-395">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="c18ce-396">I servizi sono un costo potenzialmente inferiore in scenari di infrastruttura di archiviazione di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="c18ce-396">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="c18ce-397">Per altre informazioni, vedere [Guida introduttiva: usare .NET per creare un BLOB nell'archivio oggetti](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="c18ce-397">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="c18ce-398">Scenari di caricamento di file</span><span class="sxs-lookup"><span data-stu-id="c18ce-398">File upload scenarios</span></span>

<span data-ttu-id="c18ce-399">Due approcci generali per il caricamento di file sono il buffering e il flusso.</span><span class="sxs-lookup"><span data-stu-id="c18ce-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="c18ce-400">**responseBuffering**</span><span class="sxs-lookup"><span data-stu-id="c18ce-400">**Buffering**</span></span>

<span data-ttu-id="c18ce-401">L'intero file viene letto in un oggetto <xref:Microsoft.AspNetCore.Http.IFormFile> , ovvero una rappresentazione C# del file usato per elaborare o salvare il file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="c18ce-402">Le risorse (disco, memoria) utilizzate dai caricamenti di file dipendono dal numero e dalle dimensioni dei caricamenti di file simultanei.</span><span class="sxs-lookup"><span data-stu-id="c18ce-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="c18ce-403">Se un'app tenta di memorizzare nel buffer un numero eccessivo di caricamenti, il sito si arresta in modo anomalo quando esaurisce la memoria o lo spazio su disco.</span><span class="sxs-lookup"><span data-stu-id="c18ce-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="c18ce-404">Se le dimensioni o la frequenza dei caricamenti di file esauriscono le risorse dell'app, usare il flusso.</span><span class="sxs-lookup"><span data-stu-id="c18ce-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="c18ce-405">Ogni singolo file memorizzato nel buffer che supera 64 KB viene spostato dalla memoria in un file temporaneo su disco.</span><span class="sxs-lookup"><span data-stu-id="c18ce-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="c18ce-406">Il buffering di file di piccole dimensioni viene trattato nelle sezioni seguenti di questo argomento:</span><span class="sxs-lookup"><span data-stu-id="c18ce-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="c18ce-407">Archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="c18ce-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="c18ce-408">Database</span><span class="sxs-lookup"><span data-stu-id="c18ce-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="c18ce-409">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="c18ce-409">**Streaming**</span></span>

<span data-ttu-id="c18ce-410">Il file viene ricevuto da una richiesta multipart ed elaborato o salvato direttamente dall'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="c18ce-411">Il flusso non migliora significativamente le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="c18ce-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="c18ce-412">Il flusso riduce le richieste di memoria o di spazio su disco durante il caricamento dei file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="c18ce-413">Il flusso di file di grandi dimensioni è trattato nella sezione [caricare file di grandi dimensioni con flusso](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="c18ce-414">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer all'archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="c18ce-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="c18ce-415">Per caricare file di piccole dimensioni, usare un modulo multipart o creare una richiesta POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c18ce-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="c18ce-416">L'esempio seguente illustra l'uso di un :::no-loc(Razor)::: form di pagine per caricare un singolo file ( *pages/BufferedSingleFileUploadPhysical. cshtml* nell'app di esempio):</span><span class="sxs-lookup"><span data-stu-id="c18ce-416">The following example demonstrates the use of a :::no-loc(Razor)::: Pages form to upload a single file ( *Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="c18ce-417">L'esempio seguente è analogo all'esempio precedente, ad eccezione del fatto che:</span><span class="sxs-lookup"><span data-stu-id="c18ce-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="c18ce-418">Per inviare i dati del modulo, viene usato JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="c18ce-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="c18ce-419">Nessuna convalida.</span><span class="sxs-lookup"><span data-stu-id="c18ce-419">There's no validation.</span></span>

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

<span data-ttu-id="c18ce-420">Per eseguire il POST del form in JavaScript per i client che [non supportano l'API fetch](https://caniuse.com/#feat=fetch), usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="c18ce-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="c18ce-421">Usare un riempimento di recupero (ad esempio, [Window. fetch Refill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="c18ce-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="c18ce-422">Usare `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="c18ce-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="c18ce-423">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-423">For example:</span></span>

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

<span data-ttu-id="c18ce-424">Per supportare il caricamento di file, i moduli HTML devono specificare un tipo di codifica ( `enctype` ) di `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="c18ce-425">Affinché un `files` elemento input supporti il caricamento di più file, fornire l' `multiple` attributo nell' `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="c18ce-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="c18ce-426">È possibile accedere ai singoli file caricati nel server tramite l' [associazione di modelli](xref:mvc/models/model-binding) tramite <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="c18ce-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="c18ce-427">L'app di esempio illustra più caricamenti di file memorizzati nel buffer per scenari di archiviazione di database e fisici.</span><span class="sxs-lookup"><span data-stu-id="c18ce-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="c18ce-428">Non **utilizzare la** `FileName` proprietà di <xref:Microsoft.AspNetCore.Http.IFormFile> diverso da per la visualizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="c18ce-429">Durante la visualizzazione o la registrazione, HTML codifica il nome del file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="c18ce-430">Un utente malintenzionato può fornire un nome file dannoso, inclusi percorsi completi o percorsi relativi.</span><span class="sxs-lookup"><span data-stu-id="c18ce-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="c18ce-431">Le applicazioni devono:</span><span class="sxs-lookup"><span data-stu-id="c18ce-431">Applications should:</span></span>
>
> * <span data-ttu-id="c18ce-432">Rimuovere il percorso dal nome file specificato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="c18ce-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="c18ce-433">Salvare il nome file con codifica HTML, percorso-rimosso per l'interfaccia utente o la registrazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="c18ce-434">Genera un nuovo nome file casuale per l'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="c18ce-435">Il codice seguente rimuove il percorso dal nome del file:</span><span class="sxs-lookup"><span data-stu-id="c18ce-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="c18ce-436">Gli esempi forniti finora non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="c18ce-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="c18ce-437">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="c18ce-437">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="c18ce-438">Considerazioni sulla sicurezza</span><span class="sxs-lookup"><span data-stu-id="c18ce-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="c18ce-439">Convalida</span><span class="sxs-lookup"><span data-stu-id="c18ce-439">Validation</span></span>](#validation)

<span data-ttu-id="c18ce-440">Quando si caricano file usando l'associazione di modelli e <xref:Microsoft.AspNetCore.Http.IFormFile> , il metodo di azione può accettare:</span><span class="sxs-lookup"><span data-stu-id="c18ce-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="c18ce-441">Singolo oggetto <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="c18ce-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="c18ce-442">Qualsiasi raccolta seguente che rappresenta diversi file:</span><span class="sxs-lookup"><span data-stu-id="c18ce-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="c18ce-443">[Elenco](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="c18ce-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="c18ce-444">L'associazione corrisponde ai file di form in base al nome.</span><span class="sxs-lookup"><span data-stu-id="c18ce-444">Binding matches form files by name.</span></span> <span data-ttu-id="c18ce-445">Ad esempio, il `name` valore HTML in `<input type="file" name="formFile">` deve corrispondere al parametro/proprietà di C# associato ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="c18ce-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="c18ce-446">Per ulteriori informazioni, vedere la sezione [corrispondenza del nome dell'attributo del nome del parametro del metodo post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="c18ce-447">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="c18ce-447">The following example:</span></span>

* <span data-ttu-id="c18ce-448">Esegue il ciclo di uno o più file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="c18ce-449">USA [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per restituire un percorso completo per un file, incluso il nome del file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="c18ce-450">Salva i file nella file system locale usando un nome file generato dall'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="c18ce-451">Restituisce il numero totale e le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-451">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="c18ce-452">Utilizzare `Path.GetRandomFileName` per generare un nome file senza percorso.</span><span class="sxs-lookup"><span data-stu-id="c18ce-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="c18ce-453">Nell'esempio seguente il percorso viene ottenuto dalla configurazione:</span><span class="sxs-lookup"><span data-stu-id="c18ce-453">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="c18ce-454">Il percorso passato a <xref:System.IO.FileStream> *deve* includere il nome del file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="c18ce-455">Se il nome file non viene specificato, <xref:System.UnauthorizedAccessException> viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="c18ce-456">I file caricati con la <xref:Microsoft.AspNetCore.Http.IFormFile> tecnica vengono memorizzati nel buffer in memoria o su disco sul server prima dell'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="c18ce-457">All'interno del metodo di azione, il <xref:Microsoft.AspNetCore.Http.IFormFile> contenuto è accessibile come <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="c18ce-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="c18ce-458">Oltre al file system locale, i file possono essere salvati in una condivisione di rete o in un servizio di archiviazione file, ad esempio [archiviazione BLOB di Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="c18ce-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="c18ce-459">Per un altro esempio che esegue il ciclo su più file per il caricamento e usa nomi di file sicuri, vedere *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="c18ce-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="c18ce-460">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) genera un'eccezione <xref:System.IO.IOException> se vengono creati più di 65.535 file senza eliminare i file temporanei precedenti.</span><span class="sxs-lookup"><span data-stu-id="c18ce-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="c18ce-461">Il limite di 65.535 file è un limite per server.</span><span class="sxs-lookup"><span data-stu-id="c18ce-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="c18ce-462">Per ulteriori informazioni su questo limite per il sistema operativo Windows, vedere la sezione Osservazioni negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="c18ce-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="c18ce-463">GetTempFileNameA (funzione)</span><span class="sxs-lookup"><span data-stu-id="c18ce-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="c18ce-464">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer in un database</span><span class="sxs-lookup"><span data-stu-id="c18ce-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="c18ce-465">Per archiviare i dati dei file binari in un database utilizzando [Entity Framework](/ef/core/index), definire una <xref:System.Byte> proprietà di matrice nell'entità:</span><span class="sxs-lookup"><span data-stu-id="c18ce-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="c18ce-466">Specificare una proprietà del modello di pagina per la classe che include <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="c18ce-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="c18ce-467"><xref:Microsoft.AspNetCore.Http.IFormFile> può essere usato direttamente come parametro del metodo di azione o come proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="c18ce-468">Nell'esempio precedente viene utilizzata una proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="c18ce-469">Il `FileUpload` viene utilizzato nel :::no-loc(Razor)::: form pagine:</span><span class="sxs-lookup"><span data-stu-id="c18ce-469">The `FileUpload` is used in the :::no-loc(Razor)::: Pages form:</span></span>

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

<span data-ttu-id="c18ce-470">Quando il modulo viene inviato al server, copiarlo in <xref:Microsoft.AspNetCore.Http.IFormFile> un flusso e salvarlo come una matrice di byte nel database.</span><span class="sxs-lookup"><span data-stu-id="c18ce-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="c18ce-471">Nell'esempio seguente, `_dbContext` archivia il contesto di database dell'app:</span><span class="sxs-lookup"><span data-stu-id="c18ce-471">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="c18ce-472">L'esempio precedente è simile a uno scenario illustrato nell'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="c18ce-473">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c18ce-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="c18ce-474">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="c18ce-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="c18ce-475">Quando si archiviano dati binari all'interno di database relazionali, è necessario prestare attenzione, perché l'operazione può influire negativamente sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="c18ce-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="c18ce-476">Non fare affidamento o considerare attendibile la `FileName` proprietà di <xref:Microsoft.AspNetCore.Http.IFormFile> senza convalida.</span><span class="sxs-lookup"><span data-stu-id="c18ce-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="c18ce-477">La `FileName` proprietà deve essere usata solo a scopo di visualizzazione e solo dopo la codifica HTML.</span><span class="sxs-lookup"><span data-stu-id="c18ce-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="c18ce-478">Gli esempi forniti non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="c18ce-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="c18ce-479">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="c18ce-479">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="c18ce-480">Considerazioni sulla sicurezza</span><span class="sxs-lookup"><span data-stu-id="c18ce-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="c18ce-481">Convalida</span><span class="sxs-lookup"><span data-stu-id="c18ce-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="c18ce-482">Caricare file di grandi dimensioni con flusso</span><span class="sxs-lookup"><span data-stu-id="c18ce-482">Upload large files with streaming</span></span>

<span data-ttu-id="c18ce-483">Nell'esempio seguente viene illustrato come utilizzare JavaScript per trasmettere un file a un'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="c18ce-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="c18ce-484">Il token antifalsificazione del file viene generato usando un attributo di filtro personalizzato e passato alle intestazioni HTTP del client anziché nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="c18ce-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="c18ce-485">Poiché il metodo di azione elabora direttamente i dati caricati, l'associazione del modello di modulo viene disabilitata da un altro filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="c18ce-486">All'interno dell'azione, il contenuto del form viene letto tramite un `MultipartReader`, che legge ogni singola `MultipartSection`, elaborando il file o archiviandone il contenuto, come appropriato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="c18ce-487">Una volta lette le sezioni multiparte, l'azione esegue una propria associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c18ce-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="c18ce-488">La risposta della pagina iniziale carica il form e salva un token antifalsificazione in un :::no-loc(cookie)::: (tramite l' `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` attributo).</span><span class="sxs-lookup"><span data-stu-id="c18ce-488">The initial page response loads the form and saves an antiforgery token in a :::no-loc(cookie)::: (via the `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` attribute).</span></span> <span data-ttu-id="c18ce-489">L'attributo usa il [supporto antifalsificazione](xref:security/anti-request-forgery) predefinito di ASP.NET Core per impostare un :::no-loc(cookie)::: con un token di richiesta:</span><span class="sxs-lookup"><span data-stu-id="c18ce-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a :::no-loc(cookie)::: with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute)]

<span data-ttu-id="c18ce-490">`DisableFormValueModelBindingAttribute`Viene usato per disabilitare l'associazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="c18ce-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="c18ce-491">Nell'app di esempio `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` e `DisableFormValueModelBindingAttribute` vengono applicati come filtri ai modelli di applicazione della pagina di `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` all' `Startup.ConfigureServices` uso delle [ :::no-loc(Razor)::: convenzioni delle pagine](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="c18ce-491">In the sample app, `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [:::no-loc(Razor)::: Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_Add:::no-loc(Razor):::Pages&highlight=7-10,16-19)]

<span data-ttu-id="c18ce-492">Poiché l'associazione di modelli non legge il modulo, i parametri associati al modulo non vengono associati (la query, la route e l'intestazione continuano a funzionare).</span><span class="sxs-lookup"><span data-stu-id="c18ce-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="c18ce-493">Il metodo di azione funziona direttamente con la `Request` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="c18ce-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="c18ce-494">Per leggere ogni sezione, viene usato un `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="c18ce-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="c18ce-495">I dati chiave/valore vengono archiviati in un oggetto `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="c18ce-496">Una volta lette le sezioni multipart, il contenuto di `KeyValueAccumulator` viene utilizzato per associare i dati del modulo a un tipo di modello.</span><span class="sxs-lookup"><span data-stu-id="c18ce-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="c18ce-497">Metodo completo `StreamingController.UploadDatabase` per lo streaming in un database con EF Core:</span><span class="sxs-lookup"><span data-stu-id="c18ce-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="c18ce-498">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper. cs* ):</span><span class="sxs-lookup"><span data-stu-id="c18ce-498">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="c18ce-499">Il `StreamingController.UploadPhysical` metodo completo per lo streaming in una posizione fisica:</span><span class="sxs-lookup"><span data-stu-id="c18ce-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="c18ce-500">Nell'app di esempio, i controlli di convalida vengono gestiti da `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="c18ce-501">Convalida</span><span class="sxs-lookup"><span data-stu-id="c18ce-501">Validation</span></span>

<span data-ttu-id="c18ce-502">La classe dell'app di esempio `FileHelpers` illustra alcuni controlli per i caricamenti di file memorizzati nel buffer <xref:Microsoft.AspNetCore.Http.IFormFile> e in flusso.</span><span class="sxs-lookup"><span data-stu-id="c18ce-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="c18ce-503">Per l'elaborazione dei <xref:Microsoft.AspNetCore.Http.IFormFile> caricamenti di file memorizzati nel buffer nell'app di esempio, vedere il `ProcessFormFile` metodo nel file *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="c18ce-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="c18ce-504">Per l'elaborazione di file trasmessi, vedere il `ProcessStreamedFile` Metodo nello stesso file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="c18ce-505">I metodi di elaborazione della convalida illustrati nell'app di esempio non analizzano il contenuto dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="c18ce-506">Nella maggior parte degli scenari di produzione, nel file viene usata un'API scanner di virus/malware prima di rendere disponibile il file per gli utenti o altri sistemi.</span><span class="sxs-lookup"><span data-stu-id="c18ce-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="c18ce-507">Sebbene l'esempio di argomento fornisca un esempio funzionante di tecniche di convalida, non implementare la `FileHelpers` classe in un'app di produzione a meno che non si:</span><span class="sxs-lookup"><span data-stu-id="c18ce-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="c18ce-508">Comprendere completamente l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="c18ce-509">Modificare l'implementazione in modo appropriato per l'ambiente e le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="c18ce-510">**Non implementare mai indiscriminatamente il codice di sicurezza in un'app senza soddisfare questi requisiti.**</span><span class="sxs-lookup"><span data-stu-id="c18ce-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="c18ce-511">Convalida contenuto</span><span class="sxs-lookup"><span data-stu-id="c18ce-511">Content validation</span></span>

<span data-ttu-id="c18ce-512">**Usare un'API di analisi di virus/malware di terze parti nel contenuto caricato.**</span><span class="sxs-lookup"><span data-stu-id="c18ce-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="c18ce-513">L'analisi dei file richiede le risorse del server in scenari con volumi elevati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="c18ce-514">Se le prestazioni di elaborazione delle richieste sono diminuite a causa dell'analisi dei file, provare a eseguire l'offload del lavoro di analisi in un [servizio in background](xref:fundamentals/host/hosted-services), possibilmente in un servizio in esecuzione in un server diverso dal server dell'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="c18ce-515">In genere, i file caricati vengono mantenuti in un'area in quarantena finché non vengono controllati dal programma antivirus in background.</span><span class="sxs-lookup"><span data-stu-id="c18ce-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="c18ce-516">Quando un file passa, il file viene spostato nel percorso di archiviazione file normale.</span><span class="sxs-lookup"><span data-stu-id="c18ce-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="c18ce-517">Questi passaggi vengono in genere eseguiti insieme a un record di database che indica lo stato di analisi di un file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="c18ce-518">Usando un approccio di questo tipo, l'app e il server app rimangono incentrati sulla risposta alle richieste.</span><span class="sxs-lookup"><span data-stu-id="c18ce-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="c18ce-519">Convalida dell'estensione di file</span><span class="sxs-lookup"><span data-stu-id="c18ce-519">File extension validation</span></span>

<span data-ttu-id="c18ce-520">L'estensione del file caricato deve essere verificata rispetto a un elenco di estensioni consentite.</span><span class="sxs-lookup"><span data-stu-id="c18ce-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="c18ce-521">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="c18ce-522">Convalida della firma del file</span><span class="sxs-lookup"><span data-stu-id="c18ce-522">File signature validation</span></span>

<span data-ttu-id="c18ce-523">La firma di un file è determinata dai primi byte all'inizio di un file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="c18ce-524">Questi byte possono essere usati per indicare se l'estensione corrisponde al contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="c18ce-525">L'app di esempio controlla le firme dei file per alcuni tipi di file comuni.</span><span class="sxs-lookup"><span data-stu-id="c18ce-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="c18ce-526">Nell'esempio seguente, la firma del file per un'immagine JPEG viene verificata rispetto al file:</span><span class="sxs-lookup"><span data-stu-id="c18ce-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="c18ce-527">Per ottenere ulteriori firme di file, vedere il [database delle firme file](https://www.filesignatures.net/) e le specifiche del file ufficiale.</span><span class="sxs-lookup"><span data-stu-id="c18ce-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="c18ce-528">Sicurezza del nome file</span><span class="sxs-lookup"><span data-stu-id="c18ce-528">File name security</span></span>

<span data-ttu-id="c18ce-529">Non usare mai un nome file fornito dal client per salvare un file nell'archiviazione fisica.</span><span class="sxs-lookup"><span data-stu-id="c18ce-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="c18ce-530">Creare un nome file sicuro per il file usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per creare un percorso completo (incluso il nome file) per l'archiviazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="c18ce-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="c18ce-531">:::no-loc(Razor)::: codifica HTML automaticamente i valori delle proprietà per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-531">:::no-loc(Razor)::: automatically HTML encodes property values for display.</span></span> <span data-ttu-id="c18ce-532">Il codice seguente è sicuro da usare:</span><span class="sxs-lookup"><span data-stu-id="c18ce-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="c18ce-533">Al di fuori di :::no-loc(Razor)::: , sempre il <xref:System.Net.WebUtility.HtmlEncode*> contenuto del nome file da una richiesta dell'utente.</span><span class="sxs-lookup"><span data-stu-id="c18ce-533">Outside of :::no-loc(Razor):::, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="c18ce-534">Molte implementazioni devono includere un controllo per verificare che il file esista. in caso contrario, il file viene sovrascritto da un file con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="c18ce-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="c18ce-535">Fornire logica aggiuntiva per soddisfare le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="c18ce-536">Convalida dimensioni</span><span class="sxs-lookup"><span data-stu-id="c18ce-536">Size validation</span></span>

<span data-ttu-id="c18ce-537">Limitare le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="c18ce-538">Nell'app di esempio, le dimensioni del file sono limitate a 2 MB (indicate in byte).</span><span class="sxs-lookup"><span data-stu-id="c18ce-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="c18ce-539">Il limite viene fornito tramite la [configurazione](xref:fundamentals/configuration/index) dal *:::no-loc(appsettings.json):::* file:</span><span class="sxs-lookup"><span data-stu-id="c18ce-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *:::no-loc(appsettings.json):::* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="c18ce-540">`FileSizeLimit`Viene inserito nelle `PageModel` classi:</span><span class="sxs-lookup"><span data-stu-id="c18ce-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="c18ce-541">Quando le dimensioni di un file superano il limite, il file viene rifiutato:</span><span class="sxs-lookup"><span data-stu-id="c18ce-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="c18ce-542">Corrisponde al valore dell'attributo Name al nome del parametro del metodo POST</span><span class="sxs-lookup"><span data-stu-id="c18ce-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="c18ce-543">In non :::no-loc(Razor)::: form che inviano dati del modulo o usano direttamente il nome di JavaScript `FormData` , il nome specificato nell'elemento del form o `FormData` deve corrispondere al nome del parametro nell'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="c18ce-543">In non-:::no-loc(Razor)::: forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="c18ce-544">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="c18ce-544">In the following example:</span></span>

* <span data-ttu-id="c18ce-545">Quando si usa un `<input>` elemento, l' `name` attributo viene impostato sul valore `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="c18ce-546">Quando `FormData` si usa in JavaScript, il nome viene impostato sul valore `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="c18ce-547">Usare un nome corrispondente per il parametro del metodo C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="c18ce-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="c18ce-548">Per un :::no-loc(Razor)::: metodo gestore pagina pagine denominato `Upload` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-548">For a :::no-loc(Razor)::: Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="c18ce-549">Per un metodo di azione POST controller MVC:</span><span class="sxs-lookup"><span data-stu-id="c18ce-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="c18ce-550">Configurazione del server e dell'app</span><span class="sxs-lookup"><span data-stu-id="c18ce-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="c18ce-551">Limite di lunghezza del corpo multipart</span><span class="sxs-lookup"><span data-stu-id="c18ce-551">Multipart body length limit</span></span>

<span data-ttu-id="c18ce-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> imposta il limite per la lunghezza di ogni corpo multipart.</span><span class="sxs-lookup"><span data-stu-id="c18ce-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="c18ce-553">Le sezioni del modulo che superano questo limite generano un'espressione <xref:System.IO.InvalidDataException> quando vengono analizzate.</span><span class="sxs-lookup"><span data-stu-id="c18ce-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="c18ce-554">Il valore predefinito è 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="c18ce-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="c18ce-555">Personalizzare il limite usando l' <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> impostazione in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="c18ce-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> viene usato per impostare <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="c18ce-557">In un' :::no-loc(Razor)::: app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-557">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(Razor):::Pages(options =>
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

<span data-ttu-id="c18ce-558">In un' :::no-loc(Razor)::: app pagine o in un'app MVC, applicare il filtro al modello di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="c18ce-558">In a :::no-loc(Razor)::: Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="c18ce-559">Dimensioni massime del corpo della richiesta di gheppio</span><span class="sxs-lookup"><span data-stu-id="c18ce-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="c18ce-560">Per le app ospitate da gheppio, le dimensioni massime predefinite del corpo della richiesta sono pari a 30 milioni byte, ovvero circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="c18ce-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="c18ce-561">Personalizzare il limite usando l'opzione del server [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) gheppio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="c18ce-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> viene usato per impostare [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="c18ce-563">In un' :::no-loc(Razor)::: app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-563">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(Razor):::Pages(options =>
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

<span data-ttu-id="c18ce-564">In un' :::no-loc(Razor)::: app pagine o in un'app MVC, applicare il filtro alla classe del gestore di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="c18ce-564">In a :::no-loc(Razor)::: pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="c18ce-565">Il `RequestSizeLimitAttribute` può essere applicato anche usando la [`@attribute`](xref:mvc/views/razor#attribute) :::no-loc(Razor)::: direttiva:</span><span class="sxs-lookup"><span data-stu-id="c18ce-565">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) :::no-loc(Razor)::: directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="c18ce-566">Altri limiti del gheppio</span><span class="sxs-lookup"><span data-stu-id="c18ce-566">Other Kestrel limits</span></span>

<span data-ttu-id="c18ce-567">Per le app ospitate da gheppio possono essere applicati altri limiti di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-567">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="c18ce-568">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="c18ce-568">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="c18ce-569">Frequenza dei dati di richiesta e risposta</span><span class="sxs-lookup"><span data-stu-id="c18ce-569">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="c18ce-570">IIS</span><span class="sxs-lookup"><span data-stu-id="c18ce-570">IIS</span></span>

<span data-ttu-id="c18ce-571">Il limite di richieste predefinito ( `maxAllowedContentLength` ) è 30 milioni byte, che corrisponde a circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="c18ce-571">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="c18ce-572">Personalizzare il limite nel `web.config` file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-572">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="c18ce-573">Nell'esempio seguente il limite è impostato su 50 MB (52.428.800 byte):</span><span class="sxs-lookup"><span data-stu-id="c18ce-573">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="c18ce-574">L' `maxAllowedContentLength` impostazione si applica solo a IIS.</span><span class="sxs-lookup"><span data-stu-id="c18ce-574">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="c18ce-575">Per altre informazioni, vedere [limiti `<requestLimits>` della richiesta ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="c18ce-575">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="c18ce-576">Aumentare le dimensioni massime del corpo della richiesta per la richiesta HTTP impostando <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-576">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c18ce-577">Nell'esempio seguente il limite è impostato su 50 MB (52.428.800 byte):</span><span class="sxs-lookup"><span data-stu-id="c18ce-577">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="c18ce-578">Per altre informazioni, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="c18ce-578">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="c18ce-579">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="c18ce-579">Troubleshoot</span></span>

<span data-ttu-id="c18ce-580">Di seguito sono trattati alcuni problemi comuni riscontrati durante il caricamento di file, con le soluzioni possibili corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="c18ce-580">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="c18ce-581">Errore non trovato quando viene distribuito in un server IIS</span><span class="sxs-lookup"><span data-stu-id="c18ce-581">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="c18ce-582">L'errore seguente indica che il file caricato supera la lunghezza del contenuto configurata del server:</span><span class="sxs-lookup"><span data-stu-id="c18ce-582">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="c18ce-583">Per ulteriori informazioni, vedere la sezione [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-583">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="c18ce-584">Errore di connessione</span><span class="sxs-lookup"><span data-stu-id="c18ce-584">Connection failure</span></span>

<span data-ttu-id="c18ce-585">Un errore di connessione e una connessione al server di reimpostazione probabilmente indicano che il file caricato supera le dimensioni massime del corpo della richiesta del gheppio.</span><span class="sxs-lookup"><span data-stu-id="c18ce-585">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="c18ce-586">Per ulteriori informazioni, vedere la sezione relativa alle [dimensioni massime del corpo della richiesta di Gheppio](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-586">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="c18ce-587">I limiti di connessione del client gheppio possono richiedere anche modifiche.</span><span class="sxs-lookup"><span data-stu-id="c18ce-587">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="c18ce-588">Eccezione per riferimento Null con IFormFile</span><span class="sxs-lookup"><span data-stu-id="c18ce-588">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="c18ce-589">Se il controller accetta file caricati utilizzando <xref:Microsoft.AspNetCore.Http.IFormFile> , ma il valore è `null` , verificare che il form HTML specifichi un `enctype` valore di `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-589">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="c18ce-590">Se questo attributo non è impostato sull' `<form>` elemento, il caricamento del file non viene eseguito e gli <xref:Microsoft.AspNetCore.Http.IFormFile> argomenti associati sono `null` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-590">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="c18ce-591">Verificare inoltre che la [denominazione di caricamento nei dati del modulo corrisponda alla denominazione dell'app](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="c18ce-591">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="c18ce-592">Il flusso è troppo lungo</span><span class="sxs-lookup"><span data-stu-id="c18ce-592">Stream was too long</span></span>

<span data-ttu-id="c18ce-593">Gli esempi in questo argomento si basano su <xref:System.IO.MemoryStream> per conservare il contenuto del file caricato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-593">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="c18ce-594">Il limite delle dimensioni di un oggetto `MemoryStream` è `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-594">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="c18ce-595">Se lo scenario di caricamento dei file dell'app richiede un contenuto del file di dimensioni superiori a 50 MB, usare un approccio alternativo che non si basa su un singolo `MemoryStream` per contenere il contenuto di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-595">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c18ce-596">ASP.NET Core supporta il caricamento di uno o più file mediante l'associazione di modelli memorizzati nel buffer per i file più piccoli e il flusso non memorizzato nel buffer per file di dimensioni maggiori.</span><span class="sxs-lookup"><span data-stu-id="c18ce-596">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="c18ce-597">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c18ce-597">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="c18ce-598">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="c18ce-598">Security considerations</span></span>

<span data-ttu-id="c18ce-599">Prestare attenzione quando si fornisce agli utenti la possibilità di caricare file in un server.</span><span class="sxs-lookup"><span data-stu-id="c18ce-599">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="c18ce-600">Gli utenti malintenzionati possono tentare di:</span><span class="sxs-lookup"><span data-stu-id="c18ce-600">Attackers may attempt to:</span></span>

* <span data-ttu-id="c18ce-601">Eseguire attacchi [di tipo Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-601">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="c18ce-602">Caricare virus o malware.</span><span class="sxs-lookup"><span data-stu-id="c18ce-602">Upload viruses or malware.</span></span>
* <span data-ttu-id="c18ce-603">Compromettere le reti e i server in altri modi.</span><span class="sxs-lookup"><span data-stu-id="c18ce-603">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="c18ce-604">I passaggi di sicurezza che riducono la probabilità di un attacco riuscito sono:</span><span class="sxs-lookup"><span data-stu-id="c18ce-604">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="c18ce-605">Caricare i file in un'area di caricamento file dedicata, preferibilmente in un'unità non di sistema.</span><span class="sxs-lookup"><span data-stu-id="c18ce-605">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="c18ce-606">Un percorso dedicato semplifica l'imposizione delle restrizioni di sicurezza sui file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-606">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="c18ce-607">Disabilitare le autorizzazioni di esecuzione per il percorso di caricamento del file.&dagger;</span><span class="sxs-lookup"><span data-stu-id="c18ce-607">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="c18ce-608">Non **rende permanente i** file caricati nella stessa struttura di directory dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="c18ce-608">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="c18ce-609">Usare un nome file sicuro determinato dall'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-609">Use a safe file name determined by the app.</span></span> <span data-ttu-id="c18ce-610">Non usare un nome file fornito dall'utente o il nome file non attendibile del file caricato. &dagger; HTML codifica il nome file non attendibile quando viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-610">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="c18ce-611">Ad esempio, registrare il nome del file o visualizzarlo nell'interfaccia utente (l' :::no-loc(Razor)::: output viene codificato automaticamente in HTML).</span><span class="sxs-lookup"><span data-stu-id="c18ce-611">For example, logging the file name or displaying in UI (:::no-loc(Razor)::: automatically HTML encodes output).</span></span>
* <span data-ttu-id="c18ce-612">Consenti solo le estensioni di file approvate per la specifica di progettazione dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="c18ce-612">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="c18ce-613">Verificare che i controlli lato client vengano eseguiti sul server. &dagger; I controlli lato client sono facili da aggirare.</span><span class="sxs-lookup"><span data-stu-id="c18ce-613">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="c18ce-614">Controllare le dimensioni di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-614">Check the size of an uploaded file.</span></span> <span data-ttu-id="c18ce-615">Impostare un limite di dimensioni massime per impedire caricamenti di grandi dimensioni.&dagger;</span><span class="sxs-lookup"><span data-stu-id="c18ce-615">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="c18ce-616">Quando i file non devono essere sovrascritti da un file caricato con lo stesso nome, controllare il nome del file sul database o sull'archiviazione fisica prima di caricare il file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-616">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="c18ce-617">**Eseguire uno scanner virus/malware sul contenuto caricato prima che il file venga archiviato.**</span><span class="sxs-lookup"><span data-stu-id="c18ce-617">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="c18ce-618">&dagger;L'app di esempio illustra un approccio che soddisfa i criteri.</span><span class="sxs-lookup"><span data-stu-id="c18ce-618">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="c18ce-619">Il caricamento di codice dannoso in un sistema è spesso il primo passaggio per l'esecuzione di codice in grado di:</span><span class="sxs-lookup"><span data-stu-id="c18ce-619">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="c18ce-620">Ottenere il controllo completo di un sistema.</span><span class="sxs-lookup"><span data-stu-id="c18ce-620">Completely gain control of a system.</span></span>
> * <span data-ttu-id="c18ce-621">Sovraccaricare un sistema con il risultato che si verifica un arresto anomalo del sistema.</span><span class="sxs-lookup"><span data-stu-id="c18ce-621">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="c18ce-622">Compromettere i dati di sistemi o utenti.</span><span class="sxs-lookup"><span data-stu-id="c18ce-622">Compromise user or system data.</span></span>
> * <span data-ttu-id="c18ce-623">Applicare i graffiti a un'interfaccia utente pubblica.</span><span class="sxs-lookup"><span data-stu-id="c18ce-623">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="c18ce-624">Per informazioni sulla riduzione della superficie di attacco quando si accettano file dagli utenti, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="c18ce-624">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="c18ce-625">Caricamento di file senza restrizioni</span><span class="sxs-lookup"><span data-stu-id="c18ce-625">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="c18ce-626">Protezione di Azure: Verificare i controlli appropriati quando si accettano file dagli utenti</span><span class="sxs-lookup"><span data-stu-id="c18ce-626">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="c18ce-627">Per ulteriori informazioni sull'implementazione di misure di sicurezza, inclusi esempi dall'app di esempio, vedere la sezione [convalida](#validation) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-627">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="c18ce-628">Scenari di archiviazione</span><span class="sxs-lookup"><span data-stu-id="c18ce-628">Storage scenarios</span></span>

<span data-ttu-id="c18ce-629">Le opzioni di archiviazione comuni per i file includono:</span><span class="sxs-lookup"><span data-stu-id="c18ce-629">Common storage options for files include:</span></span>

* <span data-ttu-id="c18ce-630">Database</span><span class="sxs-lookup"><span data-stu-id="c18ce-630">Database</span></span>

  * <span data-ttu-id="c18ce-631">Per i caricamenti di file di piccole dimensioni, un database è spesso più veloce rispetto alle opzioni di archiviazione fisica (file system o condivisione di rete).</span><span class="sxs-lookup"><span data-stu-id="c18ce-631">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="c18ce-632">Un database è spesso più pratico delle opzioni di archiviazione fisica perché il recupero di un record di database per i dati utente può fornire simultaneamente il contenuto del file, ad esempio un'immagine avatar.</span><span class="sxs-lookup"><span data-stu-id="c18ce-632">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="c18ce-633">Un database è potenzialmente meno costoso rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-633">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="c18ce-634">Archiviazione fisica (file system o condivisione di rete)</span><span class="sxs-lookup"><span data-stu-id="c18ce-634">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="c18ce-635">Per i caricamenti di file di grandi dimensioni:</span><span class="sxs-lookup"><span data-stu-id="c18ce-635">For large file uploads:</span></span>
    * <span data-ttu-id="c18ce-636">I limiti del database possono limitare le dimensioni del caricamento.</span><span class="sxs-lookup"><span data-stu-id="c18ce-636">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="c18ce-637">L'archiviazione fisica è spesso meno economica rispetto all'archiviazione in un database.</span><span class="sxs-lookup"><span data-stu-id="c18ce-637">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="c18ce-638">L'archiviazione fisica è potenzialmente meno costosa rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-638">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="c18ce-639">Il processo dell'app deve avere le autorizzazioni di lettura e scrittura per il percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-639">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="c18ce-640">**Non concedere mai l'autorizzazione Execute.**</span><span class="sxs-lookup"><span data-stu-id="c18ce-640">**Never grant execute permission.**</span></span>

* <span data-ttu-id="c18ce-641">Servizio di archiviazione dei dati (ad esempio, [archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="c18ce-641">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="c18ce-642">I servizi in genere offrono una maggiore scalabilità e resilienza rispetto a soluzioni locali che in genere sono soggette a singoli punti di errore.</span><span class="sxs-lookup"><span data-stu-id="c18ce-642">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="c18ce-643">I servizi sono un costo potenzialmente inferiore in scenari di infrastruttura di archiviazione di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="c18ce-643">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="c18ce-644">Per altre informazioni, vedere [Guida introduttiva: usare .NET per creare un BLOB nell'archivio oggetti](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="c18ce-644">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="c18ce-645">Nell'argomento viene illustrato <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , ma <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> è possibile utilizzare per salvare un <xref:System.IO.FileStream> nell'archivio BLOB quando si utilizza un <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="c18ce-645">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="c18ce-646">Scenari di caricamento di file</span><span class="sxs-lookup"><span data-stu-id="c18ce-646">File upload scenarios</span></span>

<span data-ttu-id="c18ce-647">Due approcci generali per il caricamento di file sono il buffering e il flusso.</span><span class="sxs-lookup"><span data-stu-id="c18ce-647">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="c18ce-648">**responseBuffering**</span><span class="sxs-lookup"><span data-stu-id="c18ce-648">**Buffering**</span></span>

<span data-ttu-id="c18ce-649">L'intero file viene letto in un oggetto <xref:Microsoft.AspNetCore.Http.IFormFile> , ovvero una rappresentazione C# del file usato per elaborare o salvare il file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-649">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="c18ce-650">Le risorse (disco, memoria) utilizzate dai caricamenti di file dipendono dal numero e dalle dimensioni dei caricamenti di file simultanei.</span><span class="sxs-lookup"><span data-stu-id="c18ce-650">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="c18ce-651">Se un'app tenta di memorizzare nel buffer un numero eccessivo di caricamenti, il sito si arresta in modo anomalo quando esaurisce la memoria o lo spazio su disco.</span><span class="sxs-lookup"><span data-stu-id="c18ce-651">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="c18ce-652">Se le dimensioni o la frequenza dei caricamenti di file esauriscono le risorse dell'app, usare il flusso.</span><span class="sxs-lookup"><span data-stu-id="c18ce-652">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="c18ce-653">Ogni singolo file memorizzato nel buffer che supera 64 KB viene spostato dalla memoria in un file temporaneo su disco.</span><span class="sxs-lookup"><span data-stu-id="c18ce-653">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="c18ce-654">Il buffering di file di piccole dimensioni viene trattato nelle sezioni seguenti di questo argomento:</span><span class="sxs-lookup"><span data-stu-id="c18ce-654">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="c18ce-655">Archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="c18ce-655">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="c18ce-656">Database</span><span class="sxs-lookup"><span data-stu-id="c18ce-656">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="c18ce-657">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="c18ce-657">**Streaming**</span></span>

<span data-ttu-id="c18ce-658">Il file viene ricevuto da una richiesta multipart ed elaborato o salvato direttamente dall'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-658">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="c18ce-659">Il flusso non migliora significativamente le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="c18ce-659">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="c18ce-660">Il flusso riduce le richieste di memoria o di spazio su disco durante il caricamento dei file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-660">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="c18ce-661">Il flusso di file di grandi dimensioni è trattato nella sezione [caricare file di grandi dimensioni con flusso](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-661">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="c18ce-662">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer all'archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="c18ce-662">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="c18ce-663">Per caricare file di piccole dimensioni, usare un modulo multipart o creare una richiesta POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c18ce-663">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="c18ce-664">L'esempio seguente illustra l'uso di un :::no-loc(Razor)::: form di pagine per caricare un singolo file ( *pages/BufferedSingleFileUploadPhysical. cshtml* nell'app di esempio):</span><span class="sxs-lookup"><span data-stu-id="c18ce-664">The following example demonstrates the use of a :::no-loc(Razor)::: Pages form to upload a single file ( *Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="c18ce-665">L'esempio seguente è analogo all'esempio precedente, ad eccezione del fatto che:</span><span class="sxs-lookup"><span data-stu-id="c18ce-665">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="c18ce-666">Per inviare i dati del modulo, viene usato JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="c18ce-666">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="c18ce-667">Nessuna convalida.</span><span class="sxs-lookup"><span data-stu-id="c18ce-667">There's no validation.</span></span>

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

<span data-ttu-id="c18ce-668">Per eseguire il POST del form in JavaScript per i client che [non supportano l'API fetch](https://caniuse.com/#feat=fetch), usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="c18ce-668">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="c18ce-669">Usare un riempimento di recupero (ad esempio, [Window. fetch Refill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="c18ce-669">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="c18ce-670">Usare `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="c18ce-670">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="c18ce-671">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-671">For example:</span></span>

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

<span data-ttu-id="c18ce-672">Per supportare il caricamento di file, i moduli HTML devono specificare un tipo di codifica ( `enctype` ) di `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-672">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="c18ce-673">Affinché un `files` elemento input supporti il caricamento di più file, fornire l' `multiple` attributo nell' `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="c18ce-673">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="c18ce-674">È possibile accedere ai singoli file caricati nel server tramite l' [associazione di modelli](xref:mvc/models/model-binding) tramite <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="c18ce-674">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="c18ce-675">L'app di esempio illustra più caricamenti di file memorizzati nel buffer per scenari di archiviazione di database e fisici.</span><span class="sxs-lookup"><span data-stu-id="c18ce-675">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="c18ce-676">Non **utilizzare la** `FileName` proprietà di <xref:Microsoft.AspNetCore.Http.IFormFile> diverso da per la visualizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-676">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="c18ce-677">Durante la visualizzazione o la registrazione, HTML codifica il nome del file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-677">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="c18ce-678">Un utente malintenzionato può fornire un nome file dannoso, inclusi percorsi completi o percorsi relativi.</span><span class="sxs-lookup"><span data-stu-id="c18ce-678">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="c18ce-679">Le applicazioni devono:</span><span class="sxs-lookup"><span data-stu-id="c18ce-679">Applications should:</span></span>
>
> * <span data-ttu-id="c18ce-680">Rimuovere il percorso dal nome file specificato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="c18ce-680">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="c18ce-681">Salvare il nome file con codifica HTML, percorso-rimosso per l'interfaccia utente o la registrazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-681">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="c18ce-682">Genera un nuovo nome file casuale per l'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-682">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="c18ce-683">Il codice seguente rimuove il percorso dal nome del file:</span><span class="sxs-lookup"><span data-stu-id="c18ce-683">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="c18ce-684">Gli esempi forniti finora non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="c18ce-684">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="c18ce-685">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="c18ce-685">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="c18ce-686">Considerazioni sulla sicurezza</span><span class="sxs-lookup"><span data-stu-id="c18ce-686">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="c18ce-687">Convalida</span><span class="sxs-lookup"><span data-stu-id="c18ce-687">Validation</span></span>](#validation)

<span data-ttu-id="c18ce-688">Quando si caricano file usando l'associazione di modelli e <xref:Microsoft.AspNetCore.Http.IFormFile> , il metodo di azione può accettare:</span><span class="sxs-lookup"><span data-stu-id="c18ce-688">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="c18ce-689">Singolo oggetto <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="c18ce-689">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="c18ce-690">Qualsiasi raccolta seguente che rappresenta diversi file:</span><span class="sxs-lookup"><span data-stu-id="c18ce-690">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="c18ce-691">[Elenco](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="c18ce-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="c18ce-692">L'associazione corrisponde ai file di form in base al nome.</span><span class="sxs-lookup"><span data-stu-id="c18ce-692">Binding matches form files by name.</span></span> <span data-ttu-id="c18ce-693">Ad esempio, il `name` valore HTML in `<input type="file" name="formFile">` deve corrispondere al parametro/proprietà di C# associato ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="c18ce-693">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="c18ce-694">Per ulteriori informazioni, vedere la sezione [corrispondenza del nome dell'attributo del nome del parametro del metodo post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-694">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="c18ce-695">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="c18ce-695">The following example:</span></span>

* <span data-ttu-id="c18ce-696">Esegue il ciclo di uno o più file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-696">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="c18ce-697">USA [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per restituire un percorso completo per un file, incluso il nome del file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-697">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="c18ce-698">Salva i file nella file system locale usando un nome file generato dall'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-698">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="c18ce-699">Restituisce il numero totale e le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-699">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="c18ce-700">Utilizzare `Path.GetRandomFileName` per generare un nome file senza percorso.</span><span class="sxs-lookup"><span data-stu-id="c18ce-700">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="c18ce-701">Nell'esempio seguente il percorso viene ottenuto dalla configurazione:</span><span class="sxs-lookup"><span data-stu-id="c18ce-701">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="c18ce-702">Il percorso passato a <xref:System.IO.FileStream> *deve* includere il nome del file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-702">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="c18ce-703">Se il nome file non viene specificato, <xref:System.UnauthorizedAccessException> viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-703">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="c18ce-704">I file caricati con la <xref:Microsoft.AspNetCore.Http.IFormFile> tecnica vengono memorizzati nel buffer in memoria o su disco sul server prima dell'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-704">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="c18ce-705">All'interno del metodo di azione, il <xref:Microsoft.AspNetCore.Http.IFormFile> contenuto è accessibile come <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="c18ce-705">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="c18ce-706">Oltre al file system locale, i file possono essere salvati in una condivisione di rete o in un servizio di archiviazione file, ad esempio [archiviazione BLOB di Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="c18ce-706">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="c18ce-707">Per un altro esempio che esegue il ciclo su più file per il caricamento e usa nomi di file sicuri, vedere *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="c18ce-707">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="c18ce-708">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) genera un'eccezione <xref:System.IO.IOException> se vengono creati più di 65.535 file senza eliminare i file temporanei precedenti.</span><span class="sxs-lookup"><span data-stu-id="c18ce-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="c18ce-709">Il limite di 65.535 file è un limite per server.</span><span class="sxs-lookup"><span data-stu-id="c18ce-709">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="c18ce-710">Per ulteriori informazioni su questo limite per il sistema operativo Windows, vedere la sezione Osservazioni negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="c18ce-710">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="c18ce-711">GetTempFileNameA (funzione)</span><span class="sxs-lookup"><span data-stu-id="c18ce-711">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="c18ce-712">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer in un database</span><span class="sxs-lookup"><span data-stu-id="c18ce-712">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="c18ce-713">Per archiviare i dati dei file binari in un database utilizzando [Entity Framework](/ef/core/index), definire una <xref:System.Byte> proprietà di matrice nell'entità:</span><span class="sxs-lookup"><span data-stu-id="c18ce-713">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="c18ce-714">Specificare una proprietà del modello di pagina per la classe che include <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="c18ce-714">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="c18ce-715"><xref:Microsoft.AspNetCore.Http.IFormFile> può essere usato direttamente come parametro del metodo di azione o come proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-715"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="c18ce-716">Nell'esempio precedente viene utilizzata una proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-716">The prior example uses a bound model property.</span></span>

<span data-ttu-id="c18ce-717">Il `FileUpload` viene utilizzato nel :::no-loc(Razor)::: form pagine:</span><span class="sxs-lookup"><span data-stu-id="c18ce-717">The `FileUpload` is used in the :::no-loc(Razor)::: Pages form:</span></span>

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

<span data-ttu-id="c18ce-718">Quando il modulo viene inviato al server, copiarlo in <xref:Microsoft.AspNetCore.Http.IFormFile> un flusso e salvarlo come una matrice di byte nel database.</span><span class="sxs-lookup"><span data-stu-id="c18ce-718">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="c18ce-719">Nell'esempio seguente, `_dbContext` archivia il contesto di database dell'app:</span><span class="sxs-lookup"><span data-stu-id="c18ce-719">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="c18ce-720">L'esempio precedente è simile a uno scenario illustrato nell'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-720">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="c18ce-721">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c18ce-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="c18ce-722">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="c18ce-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="c18ce-723">Quando si archiviano dati binari all'interno di database relazionali, è necessario prestare attenzione, perché l'operazione può influire negativamente sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="c18ce-723">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="c18ce-724">Non fare affidamento o considerare attendibile la `FileName` proprietà di <xref:Microsoft.AspNetCore.Http.IFormFile> senza convalida.</span><span class="sxs-lookup"><span data-stu-id="c18ce-724">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="c18ce-725">La `FileName` proprietà deve essere usata solo a scopo di visualizzazione e solo dopo la codifica HTML.</span><span class="sxs-lookup"><span data-stu-id="c18ce-725">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="c18ce-726">Gli esempi forniti non prendono in considerazione le considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="c18ce-726">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="c18ce-727">Le informazioni aggiuntive sono fornite nelle sezioni seguenti e nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="c18ce-727">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="c18ce-728">Considerazioni sulla sicurezza</span><span class="sxs-lookup"><span data-stu-id="c18ce-728">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="c18ce-729">Convalida</span><span class="sxs-lookup"><span data-stu-id="c18ce-729">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="c18ce-730">Caricare file di grandi dimensioni con flusso</span><span class="sxs-lookup"><span data-stu-id="c18ce-730">Upload large files with streaming</span></span>

<span data-ttu-id="c18ce-731">Nell'esempio seguente viene illustrato come utilizzare JavaScript per trasmettere un file a un'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="c18ce-731">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="c18ce-732">Il token antifalsificazione del file viene generato usando un attributo di filtro personalizzato e passato alle intestazioni HTTP del client anziché nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="c18ce-732">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="c18ce-733">Poiché il metodo di azione elabora direttamente i dati caricati, l'associazione del modello di modulo viene disabilitata da un altro filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-733">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="c18ce-734">All'interno dell'azione, il contenuto del form viene letto tramite un `MultipartReader`, che legge ogni singola `MultipartSection`, elaborando il file o archiviandone il contenuto, come appropriato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-734">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="c18ce-735">Una volta lette le sezioni multiparte, l'azione esegue una propria associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c18ce-735">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="c18ce-736">La risposta della pagina iniziale carica il form e salva un token antifalsificazione in un :::no-loc(cookie)::: (tramite l' `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` attributo).</span><span class="sxs-lookup"><span data-stu-id="c18ce-736">The initial page response loads the form and saves an antiforgery token in a :::no-loc(cookie)::: (via the `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` attribute).</span></span> <span data-ttu-id="c18ce-737">L'attributo usa il [supporto antifalsificazione](xref:security/anti-request-forgery) predefinito di ASP.NET Core per impostare un :::no-loc(cookie)::: con un token di richiesta:</span><span class="sxs-lookup"><span data-stu-id="c18ce-737">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a :::no-loc(cookie)::: with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute)]

<span data-ttu-id="c18ce-738">`DisableFormValueModelBindingAttribute`Viene usato per disabilitare l'associazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="c18ce-738">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="c18ce-739">Nell'app di esempio `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` e `DisableFormValueModelBindingAttribute` vengono applicati come filtri ai modelli di applicazione della pagina di `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` all' `Startup.ConfigureServices` uso delle [ :::no-loc(Razor)::: convenzioni delle pagine](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="c18ce-739">In the sample app, `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [:::no-loc(Razor)::: Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="c18ce-740">Poiché l'associazione di modelli non legge il modulo, i parametri associati al modulo non vengono associati (la query, la route e l'intestazione continuano a funzionare).</span><span class="sxs-lookup"><span data-stu-id="c18ce-740">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="c18ce-741">Il metodo di azione funziona direttamente con la `Request` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="c18ce-741">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="c18ce-742">Per leggere ogni sezione, viene usato un `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="c18ce-742">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="c18ce-743">I dati chiave/valore vengono archiviati in un oggetto `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-743">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="c18ce-744">Una volta lette le sezioni multipart, il contenuto di `KeyValueAccumulator` viene utilizzato per associare i dati del modulo a un tipo di modello.</span><span class="sxs-lookup"><span data-stu-id="c18ce-744">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="c18ce-745">Metodo completo `StreamingController.UploadDatabase` per lo streaming in un database con EF Core:</span><span class="sxs-lookup"><span data-stu-id="c18ce-745">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="c18ce-746">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper. cs* ):</span><span class="sxs-lookup"><span data-stu-id="c18ce-746">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="c18ce-747">Il `StreamingController.UploadPhysical` metodo completo per lo streaming in una posizione fisica:</span><span class="sxs-lookup"><span data-stu-id="c18ce-747">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="c18ce-748">Nell'app di esempio, i controlli di convalida vengono gestiti da `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-748">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="c18ce-749">Convalida</span><span class="sxs-lookup"><span data-stu-id="c18ce-749">Validation</span></span>

<span data-ttu-id="c18ce-750">La classe dell'app di esempio `FileHelpers` illustra alcuni controlli per i caricamenti di file memorizzati nel buffer <xref:Microsoft.AspNetCore.Http.IFormFile> e in flusso.</span><span class="sxs-lookup"><span data-stu-id="c18ce-750">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="c18ce-751">Per l'elaborazione dei <xref:Microsoft.AspNetCore.Http.IFormFile> caricamenti di file memorizzati nel buffer nell'app di esempio, vedere il `ProcessFormFile` metodo nel file *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="c18ce-751">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="c18ce-752">Per l'elaborazione di file trasmessi, vedere il `ProcessStreamedFile` Metodo nello stesso file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-752">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="c18ce-753">I metodi di elaborazione della convalida illustrati nell'app di esempio non analizzano il contenuto dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-753">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="c18ce-754">Nella maggior parte degli scenari di produzione, nel file viene usata un'API scanner di virus/malware prima di rendere disponibile il file per gli utenti o altri sistemi.</span><span class="sxs-lookup"><span data-stu-id="c18ce-754">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="c18ce-755">Sebbene l'esempio di argomento fornisca un esempio funzionante di tecniche di convalida, non implementare la `FileHelpers` classe in un'app di produzione a meno che non si:</span><span class="sxs-lookup"><span data-stu-id="c18ce-755">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="c18ce-756">Comprendere completamente l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-756">Fully understand the implementation.</span></span>
> * <span data-ttu-id="c18ce-757">Modificare l'implementazione in modo appropriato per l'ambiente e le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-757">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="c18ce-758">**Non implementare mai indiscriminatamente il codice di sicurezza in un'app senza soddisfare questi requisiti.**</span><span class="sxs-lookup"><span data-stu-id="c18ce-758">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="c18ce-759">Convalida contenuto</span><span class="sxs-lookup"><span data-stu-id="c18ce-759">Content validation</span></span>

<span data-ttu-id="c18ce-760">**Usare un'API di analisi di virus/malware di terze parti nel contenuto caricato.**</span><span class="sxs-lookup"><span data-stu-id="c18ce-760">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="c18ce-761">L'analisi dei file richiede le risorse del server in scenari con volumi elevati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-761">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="c18ce-762">Se le prestazioni di elaborazione delle richieste sono diminuite a causa dell'analisi dei file, provare a eseguire l'offload del lavoro di analisi in un [servizio in background](xref:fundamentals/host/hosted-services), possibilmente in un servizio in esecuzione in un server diverso dal server dell'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-762">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="c18ce-763">In genere, i file caricati vengono mantenuti in un'area in quarantena finché non vengono controllati dal programma antivirus in background.</span><span class="sxs-lookup"><span data-stu-id="c18ce-763">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="c18ce-764">Quando un file passa, il file viene spostato nel percorso di archiviazione file normale.</span><span class="sxs-lookup"><span data-stu-id="c18ce-764">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="c18ce-765">Questi passaggi vengono in genere eseguiti insieme a un record di database che indica lo stato di analisi di un file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-765">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="c18ce-766">Usando un approccio di questo tipo, l'app e il server app rimangono incentrati sulla risposta alle richieste.</span><span class="sxs-lookup"><span data-stu-id="c18ce-766">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="c18ce-767">Convalida dell'estensione di file</span><span class="sxs-lookup"><span data-stu-id="c18ce-767">File extension validation</span></span>

<span data-ttu-id="c18ce-768">L'estensione del file caricato deve essere verificata rispetto a un elenco di estensioni consentite.</span><span class="sxs-lookup"><span data-stu-id="c18ce-768">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="c18ce-769">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-769">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="c18ce-770">Convalida della firma del file</span><span class="sxs-lookup"><span data-stu-id="c18ce-770">File signature validation</span></span>

<span data-ttu-id="c18ce-771">La firma di un file è determinata dai primi byte all'inizio di un file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-771">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="c18ce-772">Questi byte possono essere usati per indicare se l'estensione corrisponde al contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-772">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="c18ce-773">L'app di esempio controlla le firme dei file per alcuni tipi di file comuni.</span><span class="sxs-lookup"><span data-stu-id="c18ce-773">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="c18ce-774">Nell'esempio seguente, la firma del file per un'immagine JPEG viene verificata rispetto al file:</span><span class="sxs-lookup"><span data-stu-id="c18ce-774">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="c18ce-775">Per ottenere ulteriori firme di file, vedere il [database delle firme file](https://www.filesignatures.net/) e le specifiche del file ufficiale.</span><span class="sxs-lookup"><span data-stu-id="c18ce-775">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="c18ce-776">Sicurezza del nome file</span><span class="sxs-lookup"><span data-stu-id="c18ce-776">File name security</span></span>

<span data-ttu-id="c18ce-777">Non usare mai un nome file fornito dal client per salvare un file nell'archiviazione fisica.</span><span class="sxs-lookup"><span data-stu-id="c18ce-777">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="c18ce-778">Creare un nome file sicuro per il file usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) per creare un percorso completo (incluso il nome file) per l'archiviazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="c18ce-778">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="c18ce-779">:::no-loc(Razor)::: codifica HTML automaticamente i valori delle proprietà per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-779">:::no-loc(Razor)::: automatically HTML encodes property values for display.</span></span> <span data-ttu-id="c18ce-780">Il codice seguente è sicuro da usare:</span><span class="sxs-lookup"><span data-stu-id="c18ce-780">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="c18ce-781">Al di fuori di :::no-loc(Razor)::: , sempre il <xref:System.Net.WebUtility.HtmlEncode*> contenuto del nome file da una richiesta dell'utente.</span><span class="sxs-lookup"><span data-stu-id="c18ce-781">Outside of :::no-loc(Razor):::, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="c18ce-782">Molte implementazioni devono includere un controllo per verificare che il file esista. in caso contrario, il file viene sovrascritto da un file con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="c18ce-782">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="c18ce-783">Fornire logica aggiuntiva per soddisfare le specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="c18ce-783">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="c18ce-784">Convalida dimensioni</span><span class="sxs-lookup"><span data-stu-id="c18ce-784">Size validation</span></span>

<span data-ttu-id="c18ce-785">Limitare le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="c18ce-785">Limit the size of uploaded files.</span></span>

<span data-ttu-id="c18ce-786">Nell'app di esempio, le dimensioni del file sono limitate a 2 MB (indicate in byte).</span><span class="sxs-lookup"><span data-stu-id="c18ce-786">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="c18ce-787">Il limite viene fornito tramite la [configurazione](xref:fundamentals/configuration/index) dal *:::no-loc(appsettings.json):::* file:</span><span class="sxs-lookup"><span data-stu-id="c18ce-787">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *:::no-loc(appsettings.json):::* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="c18ce-788">`FileSizeLimit`Viene inserito nelle `PageModel` classi:</span><span class="sxs-lookup"><span data-stu-id="c18ce-788">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="c18ce-789">Quando le dimensioni di un file superano il limite, il file viene rifiutato:</span><span class="sxs-lookup"><span data-stu-id="c18ce-789">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="c18ce-790">Corrisponde al valore dell'attributo Name al nome del parametro del metodo POST</span><span class="sxs-lookup"><span data-stu-id="c18ce-790">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="c18ce-791">In non :::no-loc(Razor)::: form che inviano dati del modulo o usano direttamente il nome di JavaScript `FormData` , il nome specificato nell'elemento del form o `FormData` deve corrispondere al nome del parametro nell'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="c18ce-791">In non-:::no-loc(Razor)::: forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="c18ce-792">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="c18ce-792">In the following example:</span></span>

* <span data-ttu-id="c18ce-793">Quando si usa un `<input>` elemento, l' `name` attributo viene impostato sul valore `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-793">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="c18ce-794">Quando `FormData` si usa in JavaScript, il nome viene impostato sul valore `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-794">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="c18ce-795">Usare un nome corrispondente per il parametro del metodo C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="c18ce-795">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="c18ce-796">Per un :::no-loc(Razor)::: metodo gestore pagina pagine denominato `Upload` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-796">For a :::no-loc(Razor)::: Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="c18ce-797">Per un metodo di azione POST controller MVC:</span><span class="sxs-lookup"><span data-stu-id="c18ce-797">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="c18ce-798">Configurazione del server e dell'app</span><span class="sxs-lookup"><span data-stu-id="c18ce-798">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="c18ce-799">Limite di lunghezza del corpo multipart</span><span class="sxs-lookup"><span data-stu-id="c18ce-799">Multipart body length limit</span></span>

<span data-ttu-id="c18ce-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> imposta il limite per la lunghezza di ogni corpo multipart.</span><span class="sxs-lookup"><span data-stu-id="c18ce-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="c18ce-801">Le sezioni del modulo che superano questo limite generano un'espressione <xref:System.IO.InvalidDataException> quando vengono analizzate.</span><span class="sxs-lookup"><span data-stu-id="c18ce-801">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="c18ce-802">Il valore predefinito è 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="c18ce-802">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="c18ce-803">Personalizzare il limite usando l' <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> impostazione in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-803">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="c18ce-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> viene usato per impostare <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="c18ce-805">In un' :::no-loc(Razor)::: app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-805">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .Add:::no-loc(Razor):::PagesOptions(options =>
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

<span data-ttu-id="c18ce-806">In un' :::no-loc(Razor)::: app pagine o in un'app MVC, applicare il filtro al modello di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="c18ce-806">In a :::no-loc(Razor)::: Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="c18ce-807">Dimensioni massime del corpo della richiesta di gheppio</span><span class="sxs-lookup"><span data-stu-id="c18ce-807">Kestrel maximum request body size</span></span>

<span data-ttu-id="c18ce-808">Per le app ospitate da gheppio, le dimensioni massime predefinite del corpo della richiesta sono pari a 30 milioni byte, ovvero circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="c18ce-808">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="c18ce-809">Personalizzare il limite usando l'opzione del server [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) gheppio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-809">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="c18ce-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> viene usato per impostare [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) per una singola pagina o un'azione.</span><span class="sxs-lookup"><span data-stu-id="c18ce-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="c18ce-811">In un' :::no-loc(Razor)::: app pagine, applicare il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c18ce-811">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .Add:::no-loc(Razor):::PagesOptions(options =>
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

<span data-ttu-id="c18ce-812">In un' :::no-loc(Razor)::: app pagine o in un'app MVC, applicare il filtro alla classe del gestore di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="c18ce-812">In a :::no-loc(Razor)::: pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="c18ce-813">Altri limiti del gheppio</span><span class="sxs-lookup"><span data-stu-id="c18ce-813">Other Kestrel limits</span></span>

<span data-ttu-id="c18ce-814">Per le app ospitate da gheppio possono essere applicati altri limiti di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="c18ce-814">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="c18ce-815">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="c18ce-815">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="c18ce-816">Frequenza dei dati di richiesta e risposta</span><span class="sxs-lookup"><span data-stu-id="c18ce-816">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="c18ce-817">IIS</span><span class="sxs-lookup"><span data-stu-id="c18ce-817">IIS</span></span>

<span data-ttu-id="c18ce-818">Il limite di richieste predefinito ( `maxAllowedContentLength` ) è 30 milioni byte, che corrisponde a circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="c18ce-818">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="c18ce-819">Personalizzare il limite nel `web.config` file.</span><span class="sxs-lookup"><span data-stu-id="c18ce-819">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="c18ce-820">Nell'esempio seguente il limite è impostato su 50 MB (52.428.800 byte):</span><span class="sxs-lookup"><span data-stu-id="c18ce-820">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="c18ce-821">L' `maxAllowedContentLength` impostazione si applica solo a IIS.</span><span class="sxs-lookup"><span data-stu-id="c18ce-821">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="c18ce-822">Per altre informazioni, vedere [limiti `<requestLimits>` della richiesta ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="c18ce-822">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="c18ce-823">Aumentare le dimensioni massime del corpo della richiesta per la richiesta HTTP impostando <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-823">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c18ce-824">Nell'esempio seguente il limite è impostato su 50 MB (52.428.800 byte):</span><span class="sxs-lookup"><span data-stu-id="c18ce-824">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="c18ce-825">Per altre informazioni, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="c18ce-825">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="c18ce-826">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="c18ce-826">Troubleshoot</span></span>

<span data-ttu-id="c18ce-827">Di seguito sono trattati alcuni problemi comuni riscontrati durante il caricamento di file, con le soluzioni possibili corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="c18ce-827">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="c18ce-828">Errore non trovato quando viene distribuito in un server IIS</span><span class="sxs-lookup"><span data-stu-id="c18ce-828">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="c18ce-829">L'errore seguente indica che il file caricato supera la lunghezza del contenuto configurata del server:</span><span class="sxs-lookup"><span data-stu-id="c18ce-829">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="c18ce-830">Per ulteriori informazioni, vedere la sezione [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-830">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="c18ce-831">Errore di connessione</span><span class="sxs-lookup"><span data-stu-id="c18ce-831">Connection failure</span></span>

<span data-ttu-id="c18ce-832">Un errore di connessione e una connessione al server di reimpostazione probabilmente indicano che il file caricato supera le dimensioni massime del corpo della richiesta del gheppio.</span><span class="sxs-lookup"><span data-stu-id="c18ce-832">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="c18ce-833">Per ulteriori informazioni, vedere la sezione relativa alle [dimensioni massime del corpo della richiesta di Gheppio](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="c18ce-833">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="c18ce-834">I limiti di connessione del client gheppio possono richiedere anche modifiche.</span><span class="sxs-lookup"><span data-stu-id="c18ce-834">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="c18ce-835">Eccezione per riferimento Null con IFormFile</span><span class="sxs-lookup"><span data-stu-id="c18ce-835">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="c18ce-836">Se il controller accetta file caricati utilizzando <xref:Microsoft.AspNetCore.Http.IFormFile> , ma il valore è `null` , verificare che il form HTML specifichi un `enctype` valore di `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-836">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="c18ce-837">Se questo attributo non è impostato sull' `<form>` elemento, il caricamento del file non viene eseguito e gli <xref:Microsoft.AspNetCore.Http.IFormFile> argomenti associati sono `null` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-837">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="c18ce-838">Verificare inoltre che la [denominazione di caricamento nei dati del modulo corrisponda alla denominazione dell'app](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="c18ce-838">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="c18ce-839">Il flusso è troppo lungo</span><span class="sxs-lookup"><span data-stu-id="c18ce-839">Stream was too long</span></span>

<span data-ttu-id="c18ce-840">Gli esempi in questo argomento si basano su <xref:System.IO.MemoryStream> per conservare il contenuto del file caricato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-840">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="c18ce-841">Il limite delle dimensioni di un oggetto `MemoryStream` è `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="c18ce-841">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="c18ce-842">Se lo scenario di caricamento dei file dell'app richiede un contenuto del file di dimensioni superiori a 50 MB, usare un approccio alternativo che non si basa su un singolo `MemoryStream` per contenere il contenuto di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="c18ce-842">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="c18ce-843">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c18ce-843">Additional resources</span></span>

* [<span data-ttu-id="c18ce-844">Svuotamento della richiesta di connessione HTTP</span><span class="sxs-lookup"><span data-stu-id="c18ce-844">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="c18ce-845">Caricamento di file senza restrizioni</span><span class="sxs-lookup"><span data-stu-id="c18ce-845">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="c18ce-846">Sicurezza di Azure: frame di sicurezza: convalida dell'input | Attenuazioni</span><span class="sxs-lookup"><span data-stu-id="c18ce-846">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="c18ce-847">Modelli di progettazione cloud di Azure: modello di chiave del posteggiatore</span><span class="sxs-lookup"><span data-stu-id="c18ce-847">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
