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
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="f9569-103">Caricare file in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9569-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="f9569-104">Di [Steve Smith](https://ardalis.com/) e [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="f9569-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f9569-105">ASP.NET Core supporta il caricamento di uno o più file tramite l'associazione di modelli memorizzati nel buffer per file più piccoli e lo streaming senza buffer per file di dimensioni maggiori.</span><span class="sxs-lookup"><span data-stu-id="f9569-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="f9569-106">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9569-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f9569-107">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="f9569-107">Security considerations</span></span>

<span data-ttu-id="f9569-108">Prestare attenzione quando si fornisce agli utenti la possibilità di caricare file su un server.</span><span class="sxs-lookup"><span data-stu-id="f9569-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="f9569-109">Gli aggressori possono tentare di:</span><span class="sxs-lookup"><span data-stu-id="f9569-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="f9569-110">Eseguire attacchi [Denial of Service.](/windows-hardware/drivers/ifs/denial-of-service)</span><span class="sxs-lookup"><span data-stu-id="f9569-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="f9569-111">Caricare virus o malware.</span><span class="sxs-lookup"><span data-stu-id="f9569-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="f9569-112">Compromettere reti e server in altri modi.</span><span class="sxs-lookup"><span data-stu-id="f9569-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="f9569-113">I passaggi di sicurezza che riducono la probabilità di un attacco riuscito sono:</span><span class="sxs-lookup"><span data-stu-id="f9569-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="f9569-114">Caricare i file in un'area di caricamento file dedicata, preferibilmente in un'unità non di sistema.</span><span class="sxs-lookup"><span data-stu-id="f9569-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="f9569-115">Una posizione dedicata semplifica l'imponinza di restrizioni di sicurezza sui file caricati.</span><span class="sxs-lookup"><span data-stu-id="f9569-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="f9569-116">Disabilitare le autorizzazioni di esecuzione per il percorso di caricamento del file.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f9569-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="f9569-117">**Non** rendere persistenti i file caricati nella stessa struttura di directory dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f9569-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="f9569-118">Usa un nome di file sicuro determinato dall'app.</span><span class="sxs-lookup"><span data-stu-id="f9569-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="f9569-119">Non utilizzare un nome di file fornito dall'utente o il nome di file non attendibile del file caricato. &dagger; HTML codificare il nome del file non attendibile durante la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="f9569-120">Ad esempio, la registrazione del nome del file o la visualizzazione nell'interfaccia utente (Razor codifica automaticamente l'output).</span><span class="sxs-lookup"><span data-stu-id="f9569-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="f9569-121">Consenti solo le estensioni di file approvate per le specifiche di progettazione dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f9569-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="f9569-122">Verificare che i controlli sul lato client vengano eseguiti sul server. &dagger; I controlli lato client sono facili da aggirare.</span><span class="sxs-lookup"><span data-stu-id="f9569-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="f9569-123">Controllare le dimensioni di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="f9569-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="f9569-124">Impostare un limite di dimensione massima per impedire caricamenti di grandi dimensioni.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f9569-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="f9569-125">Quando i file non devono essere sovrascritti da un file caricato con lo stesso nome, controllare il nome del file rispetto al database o all'archiviazione fisica prima di caricare il file.</span><span class="sxs-lookup"><span data-stu-id="f9569-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="f9569-126">**Eseguire uno scanner antivirus/malware sul contenuto caricato prima che il file venga archiviato.**</span><span class="sxs-lookup"><span data-stu-id="f9569-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="f9569-127">&dagger;L'app di esempio illustra un approccio che soddisfa i criteri.</span><span class="sxs-lookup"><span data-stu-id="f9569-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="f9569-128">Il caricamento di codice dannoso in un sistema è spesso il primo passaggio per l'esecuzione di codice in grado di:</span><span class="sxs-lookup"><span data-stu-id="f9569-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="f9569-129">Ottenere completamente il controllo di un sistema.</span><span class="sxs-lookup"><span data-stu-id="f9569-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="f9569-130">Sovraccaricare un sistema con il risultato che il sistema si arresta in modo anomalo.</span><span class="sxs-lookup"><span data-stu-id="f9569-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="f9569-131">Compromettere i dati di sistemi o utenti.</span><span class="sxs-lookup"><span data-stu-id="f9569-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="f9569-132">Applicare graffiti a un'interfaccia utente pubblica.</span><span class="sxs-lookup"><span data-stu-id="f9569-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="f9569-133">Per informazioni sulla riduzione della superficie di attacco quando si accettano file dagli utenti, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="f9569-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="f9569-134">Caricamento di file senza restrizioni</span><span class="sxs-lookup"><span data-stu-id="f9569-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="f9569-135">Protezione di Azure: Verificare i controlli appropriati quando si accettano file dagli utenti</span><span class="sxs-lookup"><span data-stu-id="f9569-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="f9569-136">Per altre informazioni sull'implementazione di misure di sicurezza, inclusi esempi dall'app di esempio, vedere la sezione [Convalida.For](#validation) more information on implementing security measures, including examples from the sample app, see the Validation section.</span><span class="sxs-lookup"><span data-stu-id="f9569-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="f9569-137">Scenari di archiviazioneStorage scenarios</span><span class="sxs-lookup"><span data-stu-id="f9569-137">Storage scenarios</span></span>

<span data-ttu-id="f9569-138">Le opzioni di archiviazione comuni per i file includono:Common storage options for files include:</span><span class="sxs-lookup"><span data-stu-id="f9569-138">Common storage options for files include:</span></span>

* <span data-ttu-id="f9569-139">Database</span><span class="sxs-lookup"><span data-stu-id="f9569-139">Database</span></span>

  * <span data-ttu-id="f9569-140">Per i caricamenti di file di piccole dimensioni, un database è spesso più veloce delle opzioni di archiviazione fisica (file system o condivisione di rete).</span><span class="sxs-lookup"><span data-stu-id="f9569-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="f9569-141">Un database è spesso più conveniente rispetto alle opzioni di archiviazione fisica perché il recupero di un record di database per i dati utente può fornire contemporaneamente il contenuto del file (ad esempio, un'immagine avatar).</span><span class="sxs-lookup"><span data-stu-id="f9569-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="f9569-142">Un database è potenzialmente meno costoso rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="f9569-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="f9569-143">Archiviazione fisica (file system o condivisione di rete)</span><span class="sxs-lookup"><span data-stu-id="f9569-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="f9569-144">Per i caricamenti di file di grandi dimensioni:</span><span class="sxs-lookup"><span data-stu-id="f9569-144">For large file uploads:</span></span>
    * <span data-ttu-id="f9569-145">I limiti del database possono limitare le dimensioni del caricamento.</span><span class="sxs-lookup"><span data-stu-id="f9569-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="f9569-146">L'archiviazione fisica è spesso meno economica dell'archiviazione in un database.</span><span class="sxs-lookup"><span data-stu-id="f9569-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="f9569-147">L'archiviazione fisica è potenzialmente meno costosa rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="f9569-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="f9569-148">Il processo dell'app deve disporre delle autorizzazioni di lettura e scrittura per il percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="f9569-149">**Non concedere mai l'autorizzazione di esecuzione.**</span><span class="sxs-lookup"><span data-stu-id="f9569-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="f9569-150">Servizio di archiviazione dati (ad esempio, Archiviazione BLOB di [Azure)](https://azure.microsoft.com/services/storage/blobs/)</span><span class="sxs-lookup"><span data-stu-id="f9569-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="f9569-151">I servizi offrono in genere una maggiore scalabilità e resilienza rispetto alle soluzioni locali che sono in genere soggette a singoli punti di errore.</span><span class="sxs-lookup"><span data-stu-id="f9569-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="f9569-152">I servizi sono potenzialmente più bassi in scenari di infrastruttura di archiviazione di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="f9569-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="f9569-153">Per altre informazioni, vedere [Guida introduttiva: Usare .NET per creare un BLOB nell'archivio oggetti.](/azure/storage/blobs/storage-quickstart-blobs-dotnet)</span><span class="sxs-lookup"><span data-stu-id="f9569-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="f9569-154">Scenari di caricamento di file</span><span class="sxs-lookup"><span data-stu-id="f9569-154">File upload scenarios</span></span>

<span data-ttu-id="f9569-155">Due approcci generali per il caricamento dei file sono il buffering e lo streaming.</span><span class="sxs-lookup"><span data-stu-id="f9569-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="f9569-156">**responseBuffering**</span><span class="sxs-lookup"><span data-stu-id="f9569-156">**Buffering**</span></span>

<span data-ttu-id="f9569-157">L'intero file viene <xref:Microsoft.AspNetCore.Http.IFormFile>letto in un oggetto , che è una rappresentazione in C, del file utilizzato per elaborare o salvare il file.</span><span class="sxs-lookup"><span data-stu-id="f9569-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="f9569-158">Le risorse (disco, memoria) utilizzate dai caricamenti di file dipendono dal numero e dalle dimensioni dei caricamenti simultanei di file.</span><span class="sxs-lookup"><span data-stu-id="f9569-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="f9569-159">Se un'app tenta di memorizzare nel buffer troppi caricamenti, il sito si blocca quando si esaurisce la memoria o lo spazio su disco.</span><span class="sxs-lookup"><span data-stu-id="f9569-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="f9569-160">Se le dimensioni o la frequenza dei caricamenti di file stanno esaurendo le risorse dell'app, usa lo streaming.</span><span class="sxs-lookup"><span data-stu-id="f9569-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="f9569-161">Qualsiasi singolo file memorizzato nel buffer superiore a 64 KB viene spostato dalla memoria a un file temporaneo su disco.</span><span class="sxs-lookup"><span data-stu-id="f9569-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="f9569-162">Il buffering di file di piccole dimensioni è illustrato nelle sezioni seguenti di questo argomento:</span><span class="sxs-lookup"><span data-stu-id="f9569-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="f9569-163">Archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="f9569-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="f9569-164">Database</span><span class="sxs-lookup"><span data-stu-id="f9569-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="f9569-165">**Flusso**</span><span class="sxs-lookup"><span data-stu-id="f9569-165">**Streaming**</span></span>

<span data-ttu-id="f9569-166">Il file viene ricevuto da una richiesta multiparte ed elaborato direttamente o salvato dall'app.</span><span class="sxs-lookup"><span data-stu-id="f9569-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="f9569-167">Lo streaming non migliora in modo significativo le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="f9569-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="f9569-168">Lo streaming riduce le richieste di memoria o spazio su disco durante il caricamento dei file.</span><span class="sxs-lookup"><span data-stu-id="f9569-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="f9569-169">Lo streaming di file di grandi dimensioni è illustrato nella sezione Caricare file di [grandi dimensioni con lo streaming.](#upload-large-files-with-streaming)</span><span class="sxs-lookup"><span data-stu-id="f9569-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="f9569-170">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer nell'archiviazione fisicaUpload small files with buffered model binding to physical storage</span><span class="sxs-lookup"><span data-stu-id="f9569-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="f9569-171">Per caricare file di piccole dimensioni, utilizzare un modulo multiparte o costruire una richiesta POST utilizzando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f9569-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="f9569-172">L'esempio seguente illustra l'uso di un modulo Pagine Razor per caricare un singolo file (*Pages/BufferedSingleFileUploadPhysical.cshtml* nell'app di esempio):</span><span class="sxs-lookup"><span data-stu-id="f9569-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="f9569-173">L'esempio seguente è analogo all'esempio precedente, ad eccezione del fatto che:</span><span class="sxs-lookup"><span data-stu-id="f9569-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="f9569-174">JavaScript ([API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API)) viene utilizzato per inviare i dati del modulo.</span><span class="sxs-lookup"><span data-stu-id="f9569-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="f9569-175">Non c'è convalida.</span><span class="sxs-lookup"><span data-stu-id="f9569-175">There's no validation.</span></span>

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

<span data-ttu-id="f9569-176">Per eseguire il modulo POST in JavaScript per i client che [non supportano l'API Fetch](https://caniuse.com/#feat=fetch), utilizzare uno dei seguenti approcci:</span><span class="sxs-lookup"><span data-stu-id="f9569-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="f9569-177">Utilizzare un polyfill Fetch (ad esempio, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="f9569-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="f9569-178">Usare `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="f9569-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="f9569-179">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f9569-179">For example:</span></span>

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

<span data-ttu-id="f9569-180">Per supportare i caricamenti di file, i`enctype`moduli `multipart/form-data`HTML devono specificare un tipo di codifica ( ) di .</span><span class="sxs-lookup"><span data-stu-id="f9569-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="f9569-181">Per `files` un elemento di input per `multiple` supportare `<input>` il caricamento di più file forniscono l'attributo sull'elemento:For a input element to support uploading multiple files provide the attribute on the element:</span><span class="sxs-lookup"><span data-stu-id="f9569-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="f9569-182">È possibile accedere ai singoli file caricati <xref:Microsoft.AspNetCore.Http.IFormFile>sul server tramite [L'associazione di](xref:mvc/models/model-binding) modelli utilizzando .</span><span class="sxs-lookup"><span data-stu-id="f9569-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="f9569-183">L'app di esempio illustra più caricamenti di file con buffer per scenari di archiviazione fisica e di database.</span><span class="sxs-lookup"><span data-stu-id="f9569-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="f9569-184">**Non** utilizzare `FileName` la <xref:Microsoft.AspNetCore.Http.IFormFile> proprietà other than per la visualizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="f9569-185">Durante la visualizzazione o la registrazione, IL codice HTML codifica il nome del file.</span><span class="sxs-lookup"><span data-stu-id="f9569-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="f9569-186">Un utente malintenzionato può fornire un nome file dannoso, inclusi i percorsi completi o relativi.</span><span class="sxs-lookup"><span data-stu-id="f9569-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="f9569-187">Le domande devono:</span><span class="sxs-lookup"><span data-stu-id="f9569-187">Applications should:</span></span>
>
> * <span data-ttu-id="f9569-188">Rimuovere il percorso dal nome file fornito dall'utente.</span><span class="sxs-lookup"><span data-stu-id="f9569-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="f9569-189">Salvare il nome del file con codifica HTML rimosso e rimosso dal percorso per l'interfaccia utente o la registrazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="f9569-190">Generare un nuovo nome file casuale per l'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="f9569-191">Il codice seguente rimuove il percorso dal nome del file:</span><span class="sxs-lookup"><span data-stu-id="f9569-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="f9569-192">Gli esempi forniti finora non tengono conto delle considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="f9569-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="f9569-193">Ulteriori informazioni vengono fornite dalle sezioni seguenti e [dall'app di esempio:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)</span><span class="sxs-lookup"><span data-stu-id="f9569-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f9569-194">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="f9569-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f9569-195">Validation</span><span class="sxs-lookup"><span data-stu-id="f9569-195">Validation</span></span>](#validation)

<span data-ttu-id="f9569-196">Quando si caricano file <xref:Microsoft.AspNetCore.Http.IFormFile>utilizzando l'associazione di modelli e , il metodo di azione può accettare:</span><span class="sxs-lookup"><span data-stu-id="f9569-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="f9569-197">Un <xref:Microsoft.AspNetCore.Http.IFormFile>singolo file .</span><span class="sxs-lookup"><span data-stu-id="f9569-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="f9569-198">Una delle seguenti raccolte che rappresentano diversi file:</span><span class="sxs-lookup"><span data-stu-id="f9569-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="f9569-199">[Elenco](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="f9569-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="f9569-200">L'associazione corrisponde ai file del modulo in base al nome.</span><span class="sxs-lookup"><span data-stu-id="f9569-200">Binding matches form files by name.</span></span> <span data-ttu-id="f9569-201">Ad esempio, `name` il `<input type="file" name="formFile">` valore HTML in deve corrispondere`FormFile`al parametro/proprietà di C, associato a ( ).</span><span class="sxs-lookup"><span data-stu-id="f9569-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="f9569-202">Per altre informazioni, vedere la sezione Corrispondenza valore [dell'attributo Nome con nome del metodo POST.](#match-name-attribute-value-to-parameter-name-of-post-method)</span><span class="sxs-lookup"><span data-stu-id="f9569-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="f9569-203">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="f9569-203">The following example:</span></span>

* <span data-ttu-id="f9569-204">Scorre in ciclo uno o più file caricati.</span><span class="sxs-lookup"><span data-stu-id="f9569-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="f9569-205">Utilizza [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) per restituire un percorso completo per un file, incluso il nome del file.</span><span class="sxs-lookup"><span data-stu-id="f9569-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="f9569-206">Salva i file nel file system locale utilizzando un nome di file generato dall'app.</span><span class="sxs-lookup"><span data-stu-id="f9569-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="f9569-207">Restituisce il numero totale e le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="f9569-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="f9569-208">Utilizzare `Path.GetRandomFileName` per generare un nome di file senza un percorso.</span><span class="sxs-lookup"><span data-stu-id="f9569-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="f9569-209">Nell'esempio seguente, il percorso viene ottenuto dalla configurazione:In the following example, the path is obtained from configuration:</span><span class="sxs-lookup"><span data-stu-id="f9569-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="f9569-210">Il percorso passato <xref:System.IO.FileStream> all'oggetto *deve* includere il nome del file.</span><span class="sxs-lookup"><span data-stu-id="f9569-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="f9569-211">Se il nome del file <xref:System.UnauthorizedAccessException> non viene fornito, viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="f9569-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="f9569-212">I file caricati utilizzando la <xref:Microsoft.AspNetCore.Http.IFormFile> tecnica vengono memorizzati nel buffer in memoria o su disco sul server prima dell'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="f9569-213">All'interno del <xref:Microsoft.AspNetCore.Http.IFormFile> metodo di azione, il contenuto è accessibile come <xref:System.IO.Stream>oggetto .</span><span class="sxs-lookup"><span data-stu-id="f9569-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="f9569-214">Oltre al file system locale, i file possono essere salvati in una condivisione di rete o in un servizio di archiviazione file, ad esempio [Archiviazione BLOB](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)di Azure .</span><span class="sxs-lookup"><span data-stu-id="f9569-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="f9569-215">Per un altro esempio che esegue un ciclo su più file per il caricamento e usa nomi di file sicuri, vedere *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="f9569-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="f9569-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) genera <xref:System.IO.IOException> un'eccezione se vengono creati più di 65.535 file senza eliminare i file temporanei precedenti.</span><span class="sxs-lookup"><span data-stu-id="f9569-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="f9569-217">Il limite di 65.535 file è un limite per server.</span><span class="sxs-lookup"><span data-stu-id="f9569-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="f9569-218">Per ulteriori informazioni su questo limite nel sistema operativo Windows, vedere le osservazioni nei seguenti argomenti:</span><span class="sxs-lookup"><span data-stu-id="f9569-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="f9569-219">GetTempFileNameA (funzione)</span><span class="sxs-lookup"><span data-stu-id="f9569-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="f9569-220">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer in un databaseUpload small files with buffered model binding to a database</span><span class="sxs-lookup"><span data-stu-id="f9569-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="f9569-221">Per archiviare i dati di file binari <xref:System.Byte> in un database utilizzando Entity [Framework](/ef/core/index), definire una proprietà di matrice nell'entità:</span><span class="sxs-lookup"><span data-stu-id="f9569-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="f9569-222">Specificare una proprietà del modello <xref:Microsoft.AspNetCore.Http.IFormFile>di pagina per la classe che include un:</span><span class="sxs-lookup"><span data-stu-id="f9569-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="f9569-223"><xref:Microsoft.AspNetCore.Http.IFormFile>può essere utilizzato direttamente come parametro del metodo di azione o come proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="f9569-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="f9569-224">Nell'esempio precedente viene utilizzata una proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="f9569-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="f9569-225">Viene `FileUpload` utilizzato nel modulo Pagine razor:</span><span class="sxs-lookup"><span data-stu-id="f9569-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="f9569-226">Quando il modulo è POSTed al <xref:Microsoft.AspNetCore.Http.IFormFile> server, copiare il in un flusso e salvarlo come matrice di byte nel database.</span><span class="sxs-lookup"><span data-stu-id="f9569-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="f9569-227">Nell'esempio seguente `_dbContext` viene archiviato il contesto di database dell'app:In the following example, stores the app's database context:</span><span class="sxs-lookup"><span data-stu-id="f9569-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="f9569-228">L'esempio precedente è simile a uno scenario illustrato nell'app di esempio:The preceding example is similar to a scenario demonstrated in the sample app:</span><span class="sxs-lookup"><span data-stu-id="f9569-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="f9569-229">*Pagine/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f9569-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="f9569-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f9569-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="f9569-231">Quando si archiviano dati binari all'interno di database relazionali, è necessario prestare attenzione, perché l'operazione può influire negativamente sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="f9569-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="f9569-232">Non fare affidamento o `FileName` considerare <xref:Microsoft.AspNetCore.Http.IFormFile> attendibile la proprietà di senza convalida.</span><span class="sxs-lookup"><span data-stu-id="f9569-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f9569-233">La `FileName` proprietà deve essere utilizzata solo per scopi di visualizzazione e solo dopo la codifica HTML.</span><span class="sxs-lookup"><span data-stu-id="f9569-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="f9569-234">Gli esempi forniti non tengono conto delle considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="f9569-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="f9569-235">Ulteriori informazioni vengono fornite dalle sezioni seguenti e [dall'app di esempio:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)</span><span class="sxs-lookup"><span data-stu-id="f9569-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f9569-236">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="f9569-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f9569-237">Validation</span><span class="sxs-lookup"><span data-stu-id="f9569-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="f9569-238">Caricare file di grandi dimensioni con lo streaming</span><span class="sxs-lookup"><span data-stu-id="f9569-238">Upload large files with streaming</span></span>

<span data-ttu-id="f9569-239">Nell'esempio seguente viene illustrato come utilizzare JavaScript per trasmettere un file a un'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="f9569-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="f9569-240">Il token antifalandria del file viene generato utilizzando un attributo di filtro personalizzato e passato alle intestazioni HTTP del client anziché nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="f9569-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="f9569-241">Poiché il metodo di azione elabora direttamente i dati caricati, l'associazione del modello di modulo è disabilitata da un altro filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="f9569-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="f9569-242">All'interno dell'azione, il contenuto del form viene letto tramite un `MultipartReader`, che legge ogni singola `MultipartSection`, elaborando il file o archiviandone il contenuto, come appropriato.</span><span class="sxs-lookup"><span data-stu-id="f9569-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="f9569-243">Dopo la lettura delle sezioni multiparte, l'azione esegue la propria associazione di modello.</span><span class="sxs-lookup"><span data-stu-id="f9569-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="f9569-244">La risposta iniziale della pagina carica il modulo e salva `GenerateAntiforgeryTokenCookieAttribute` un token antifalsificazione in un cookie (tramite l'attributo).</span><span class="sxs-lookup"><span data-stu-id="f9569-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="f9569-245">L'attributo utilizza ASP.NET [supporto antifasciacquario](xref:security/anti-request-forgery) incorporato di Core per impostare un cookie con un token di richiesta:The attribute uses ASP.NET Core's built-in antiforgery support to set a cookie with a request token:</span><span class="sxs-lookup"><span data-stu-id="f9569-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="f9569-246">L'oggetto viene utilizzato per disabilitare l'associazione di modelli:The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span><span class="sxs-lookup"><span data-stu-id="f9569-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="f9569-247">Nell'app di `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` esempio e vengono applicati come `/StreamedSingleFileUploadDb` filtri `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` ai modelli di applicazione pagina di e tramite [le convenzioni Razor Pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="f9569-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="f9569-248">Poiché l'associazione di modelli non legge il form, i parametri associati dal form non vengono associati (query, route e intestazione continuano a funzionare).</span><span class="sxs-lookup"><span data-stu-id="f9569-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="f9569-249">Il metodo di azione `Request` funziona direttamente con la proprietà.</span><span class="sxs-lookup"><span data-stu-id="f9569-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="f9569-250">Per leggere ogni sezione, viene usato un `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="f9569-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="f9569-251">I dati chiave/valore `KeyValueAccumulator`vengono memorizzati in un file .</span><span class="sxs-lookup"><span data-stu-id="f9569-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="f9569-252">Dopo la lettura delle sezioni multiparte, il contenuto di `KeyValueAccumulator` vengono utilizzati per associare i dati del form a un tipo di modello.</span><span class="sxs-lookup"><span data-stu-id="f9569-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="f9569-253">Il `StreamingController.UploadDatabase` metodo completo per lo streaming in un database con EF Core:The complete method for streaming to a database with EF Core:</span><span class="sxs-lookup"><span data-stu-id="f9569-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="f9569-254">`MultipartRequestHelper`(*Utilità/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="f9569-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="f9569-255">Il `StreamingController.UploadPhysical` metodo completo per lo streaming in una posizione fisica:</span><span class="sxs-lookup"><span data-stu-id="f9569-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="f9569-256">Nell'app di esempio, i `FileHelpers.ProcessStreamedFile`controlli di convalida vengono gestiti da .</span><span class="sxs-lookup"><span data-stu-id="f9569-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="f9569-257">Convalida</span><span class="sxs-lookup"><span data-stu-id="f9569-257">Validation</span></span>

<span data-ttu-id="f9569-258">La classe dell'app di `FileHelpers` esempio illustra <xref:Microsoft.AspNetCore.Http.IFormFile> diversi controlli per i caricamenti di file memorizzati nel buffer e trasmessi tè.</span><span class="sxs-lookup"><span data-stu-id="f9569-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="f9569-259">Per <xref:Microsoft.AspNetCore.Http.IFormFile> elaborare i caricamenti di file `ProcessFormFile` memorizzati nel buffer nell'app di esempio, vedere il metodo nel file *Utilities/FileHelpers.cs.For* processing buffered file uploads in the sample app, see the method in the Utilities/FileHelpers.cs file.</span><span class="sxs-lookup"><span data-stu-id="f9569-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="f9569-260">Per l'elaborazione di `ProcessStreamedFile` file trasmessi, vedere il metodo nello stesso file.</span><span class="sxs-lookup"><span data-stu-id="f9569-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="f9569-261">I metodi di elaborazione della convalida illustrati nell'app di esempio non analizzano il contenuto dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="f9569-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="f9569-262">Nella maggior parte degli scenari di produzione, un'API dello scanner antivirus/malware viene utilizzata nel file prima di rendere il file disponibile agli utenti o ad altri sistemi.</span><span class="sxs-lookup"><span data-stu-id="f9569-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="f9569-263">Anche se l'esempio di argomento fornisce un esempio `FileHelpers` funzionante di tecniche di convalida, non implementare la classe in un'app di produzione a meno che non si:Although the topic sample provides a working example of validation techniques, don't implement the class in a production app unless you:</span><span class="sxs-lookup"><span data-stu-id="f9569-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="f9569-264">Comprendere appieno l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="f9569-265">Modificare l'implementazione in base all'ambiente e alle specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="f9569-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="f9569-266">**Non implementare mai indiscriminatamente il codice di sicurezza in un'app senza soddisfare questi requisiti.**</span><span class="sxs-lookup"><span data-stu-id="f9569-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="f9569-267">Convalida contenuto</span><span class="sxs-lookup"><span data-stu-id="f9569-267">Content validation</span></span>

<span data-ttu-id="f9569-268">**Utilizzare un'API di scansione antivirus/malware di terze parti sul contenuto caricato.**</span><span class="sxs-lookup"><span data-stu-id="f9569-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="f9569-269">L'analisi dei file richiede risorse del server in scenari con volumi elevati.</span><span class="sxs-lookup"><span data-stu-id="f9569-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="f9569-270">Se le prestazioni di elaborazione delle richieste diminuiscono a causa dell'analisi dei file, è consigliabile scaricare il lavoro di scansione in un [servizio in background,](xref:fundamentals/host/hosted-services)possibilmente un servizio in esecuzione su un server diverso dal server dell'app.</span><span class="sxs-lookup"><span data-stu-id="f9569-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="f9569-271">In genere, i file caricati vengono mantenuti in un'area in quarantena fino a quando non vengono esaminati dall'utilità di analisi antivirus in background.</span><span class="sxs-lookup"><span data-stu-id="f9569-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="f9569-272">Quando un file passa, il file viene spostato nel percorso di archiviazione del file normale.</span><span class="sxs-lookup"><span data-stu-id="f9569-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="f9569-273">Questi passaggi vengono in genere eseguiti insieme a un record di database che indica lo stato di analisi di un file.</span><span class="sxs-lookup"><span data-stu-id="f9569-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="f9569-274">Utilizzando tale approccio, l'app e il server applicazioni rimangono concentrati sulla risposta alle richieste.</span><span class="sxs-lookup"><span data-stu-id="f9569-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="f9569-275">Convalida dell'estensione file</span><span class="sxs-lookup"><span data-stu-id="f9569-275">File extension validation</span></span>

<span data-ttu-id="f9569-276">L'estensione del file caricato deve essere confrontata con un elenco di estensioni consentite.</span><span class="sxs-lookup"><span data-stu-id="f9569-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="f9569-277">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f9569-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="f9569-278">Convalida della firma del file</span><span class="sxs-lookup"><span data-stu-id="f9569-278">File signature validation</span></span>

<span data-ttu-id="f9569-279">La firma di un file è determinata dai primi byte all'inizio di un file.</span><span class="sxs-lookup"><span data-stu-id="f9569-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="f9569-280">Questi byte possono essere utilizzati per indicare se l'estensione corrisponde al contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="f9569-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="f9569-281">L'app di esempio controlla le firme dei file per alcuni tipi di file comuni.</span><span class="sxs-lookup"><span data-stu-id="f9569-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="f9569-282">Nell'esempio seguente, la firma del file per un'immagine JPEG viene confrontata con il file:</span><span class="sxs-lookup"><span data-stu-id="f9569-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="f9569-283">Per ottenere ulteriori firme di file, vedere il [Database delle firme](https://www.filesignatures.net/) dei file e le specifiche ufficiali dei file.</span><span class="sxs-lookup"><span data-stu-id="f9569-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="f9569-284">Sicurezza del nome file</span><span class="sxs-lookup"><span data-stu-id="f9569-284">File name security</span></span>

<span data-ttu-id="f9569-285">Non utilizzare mai un nome di file fornito dal client per salvare un file nell'archivio fisico.</span><span class="sxs-lookup"><span data-stu-id="f9569-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="f9569-286">Creare un nome di file sicuro per il file utilizzando [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) per creare un percorso completo (incluso il nome file) per l'archiviazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="f9569-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="f9569-287">Razor codifica automaticamente i valori delle proprietà per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="f9569-288">Il codice seguente è sicuro da usare:The following code is safe to use:</span><span class="sxs-lookup"><span data-stu-id="f9569-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="f9569-289">Al di fuori <xref:System.Net.WebUtility.HtmlEncode*> di Razor, il contenuto del nome file da una richiesta dell'utente.</span><span class="sxs-lookup"><span data-stu-id="f9569-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="f9569-290">Molte implementazioni devono includere un controllo che il file esista; in caso contrario, il file viene sovrascritto da un file con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="f9569-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="f9569-291">Fornisci logica aggiuntiva per soddisfare le specifiche della tua app.</span><span class="sxs-lookup"><span data-stu-id="f9569-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="f9569-292">Convalida delle dimensioni</span><span class="sxs-lookup"><span data-stu-id="f9569-292">Size validation</span></span>

<span data-ttu-id="f9569-293">Limitare le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="f9569-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="f9569-294">Nell'app di esempio, la dimensione del file è limitata a 2 MB (indicata in byte).</span><span class="sxs-lookup"><span data-stu-id="f9569-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="f9569-295">Il limite viene fornito tramite [Configuration](xref:fundamentals/configuration/index) dal file *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="f9569-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="f9569-296">Il `FileSizeLimit` viene iniettato nelle `PageModel` classi:</span><span class="sxs-lookup"><span data-stu-id="f9569-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="f9569-297">Quando una dimensione del file supera il limite, il file viene rifiutato:</span><span class="sxs-lookup"><span data-stu-id="f9569-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="f9569-298">Corrispondenza valore attributo nome al nome del metodo POST</span><span class="sxs-lookup"><span data-stu-id="f9569-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="f9569-299">Nei moduli non Razor che formano dati POST `FormData` o utilizzano direttamente JavaScript, `FormData` il nome specificato nell'elemento del modulo o deve corrispondere al nome del parametro nell'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="f9569-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="f9569-300">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="f9569-300">In the following example:</span></span>

* <span data-ttu-id="f9569-301">Quando si `<input>` utilizza `name` un elemento, l'attributo viene impostato sul valore `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="f9569-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="f9569-302">Quando `FormData` si utilizza in JavaScript, il `battlePlans`nome è impostato sul valore :</span><span class="sxs-lookup"><span data-stu-id="f9569-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="f9569-303">Utilizzare un nome corrispondente per il parametro del metodo C , (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="f9569-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="f9569-304">Per un metodo del gestore di pagina Razor Pages denominato `Upload`:</span><span class="sxs-lookup"><span data-stu-id="f9569-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="f9569-305">Per un metodo di azione del controller MVC POST:For an MVC POST controller action method:</span><span class="sxs-lookup"><span data-stu-id="f9569-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="f9569-306">Configurazione di server e app</span><span class="sxs-lookup"><span data-stu-id="f9569-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="f9569-307">Limite di lunghezza del corpo multiparte</span><span class="sxs-lookup"><span data-stu-id="f9569-307">Multipart body length limit</span></span>

<span data-ttu-id="f9569-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>imposta il limite per la lunghezza di ogni corpo multiparte.</span><span class="sxs-lookup"><span data-stu-id="f9569-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="f9569-309">Le sezioni del modulo <xref:System.IO.InvalidDataException> che superano questo limite generano un errore quando vengono analizzate.</span><span class="sxs-lookup"><span data-stu-id="f9569-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="f9569-310">Il valore predefinito è 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="f9569-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="f9569-311">Personalizzare il limite <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> utilizzando `Startup.ConfigureServices`l'impostazione in:</span><span class="sxs-lookup"><span data-stu-id="f9569-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f9569-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>viene utilizzato per <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> impostare l'oggetto per una singola pagina o azione.</span><span class="sxs-lookup"><span data-stu-id="f9569-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="f9569-313">In un'app Razor Pages, applica `Startup.ConfigureServices`il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in:</span><span class="sxs-lookup"><span data-stu-id="f9569-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f9569-314">In un'app Razor Pages o in un'app MVC applicare il filtro al modello di pagina o al metodo di azione:In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span><span class="sxs-lookup"><span data-stu-id="f9569-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="f9569-315">Dimensione massima del corpo della richiesta del gheccello</span><span class="sxs-lookup"><span data-stu-id="f9569-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="f9569-316">Per le app ospitate da Kestrel, la dimensione massima predefinita del corpo della richiesta è 30.000.000 byte, ovvero circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="f9569-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f9569-317">Personalizzare il limite utilizzando l'opzione server [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="f9569-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="f9569-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>viene utilizzato per impostare il [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) per una singola pagina o azione.</span><span class="sxs-lookup"><span data-stu-id="f9569-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="f9569-319">In un'app Razor Pages, applica `Startup.ConfigureServices`il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in:</span><span class="sxs-lookup"><span data-stu-id="f9569-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f9569-320">In un'app di pagine Razor o in un'app MVC, applica il filtro alla classe del gestore di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="f9569-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="f9569-321">Il `RequestSizeLimitAttribute` può essere applicato [`@attribute`](xref:mvc/views/razor#attribute) anche utilizzando la direttiva Razor:</span><span class="sxs-lookup"><span data-stu-id="f9569-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="f9569-322">Altri limiti di Kestrel</span><span class="sxs-lookup"><span data-stu-id="f9569-322">Other Kestrel limits</span></span>

<span data-ttu-id="f9569-323">Altri limiti di Kestrel possono richiedere le app ospitate da Kestrel:</span><span class="sxs-lookup"><span data-stu-id="f9569-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="f9569-324">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="f9569-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="f9569-325">Frequenza dei dati di richiesta e risposta</span><span class="sxs-lookup"><span data-stu-id="f9569-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="f9569-326">Limite di lunghezza del contenuto IIS</span><span class="sxs-lookup"><span data-stu-id="f9569-326">IIS content length limit</span></span>

<span data-ttu-id="f9569-327">Il limite di`maxAllowedContentLength`richieste predefinito ( ) è 30.000.000 byte, ovvero circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="f9569-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="f9569-328">Personalizzare il limite nel file *web.config:*</span><span class="sxs-lookup"><span data-stu-id="f9569-328">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="f9569-329">Questa impostazione si applica solo a IIS.</span><span class="sxs-lookup"><span data-stu-id="f9569-329">This setting only applies to IIS.</span></span> <span data-ttu-id="f9569-330">Il comportamento non si verifica per impostazione predefinita con l'hosting in Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f9569-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="f9569-331">Per ulteriori informazioni, vedere [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="f9569-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="f9569-332">Limitazioni nel ASP.NET Core Module o la presenza del modulo filtro richieste IIS può limitare i caricamenti a 2 o 4 GB.</span><span class="sxs-lookup"><span data-stu-id="f9569-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="f9569-333">Per ulteriori informazioni, vedere [Impossibile caricare file di dimensioni superiori a 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="f9569-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f9569-334">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="f9569-334">Troubleshoot</span></span>

<span data-ttu-id="f9569-335">Di seguito sono trattati alcuni problemi comuni riscontrati durante il caricamento di file, con le soluzioni possibili corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="f9569-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="f9569-336">Errore Non trovato quando viene distribuito in un server IIS</span><span class="sxs-lookup"><span data-stu-id="f9569-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="f9569-337">Il seguente errore indica che il file caricato supera la lunghezza del contenuto configurato del server:</span><span class="sxs-lookup"><span data-stu-id="f9569-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="f9569-338">Per ulteriori informazioni sull'aumento del limite, vedere la sezione Limite di [lunghezza del contenuto di IIS.](#iis-content-length-limit)</span><span class="sxs-lookup"><span data-stu-id="f9569-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="f9569-339">Errore di connessione</span><span class="sxs-lookup"><span data-stu-id="f9569-339">Connection failure</span></span>

<span data-ttu-id="f9569-340">Un errore di connessione e una connessione al server di reimpostazione indicaprobabilmente che il file caricato supera la dimensione massima del corpo della richiesta di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f9569-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="f9569-341">Per altre informazioni, vedere la sezione [Dimensioni massime del corpo della richiesta di Kestrel.For](#kestrel-maximum-request-body-size) more information, see the Kestrel maximum request body size section.</span><span class="sxs-lookup"><span data-stu-id="f9569-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="f9569-342">Anche i limiti di connessione del client Kestrel possono richiedere modifiche.</span><span class="sxs-lookup"><span data-stu-id="f9569-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="f9569-343">Eccezione per riferimento Null con IFormFile</span><span class="sxs-lookup"><span data-stu-id="f9569-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="f9569-344">Se il controller accetta i <xref:Microsoft.AspNetCore.Http.IFormFile> file caricati utilizzando ma il valore `null` `enctype` è `multipart/form-data`, verificare che il modulo HTML specifichi un valore di .</span><span class="sxs-lookup"><span data-stu-id="f9569-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="f9569-345">Se questo attributo non `<form>` è impostato sull'elemento, il caricamento <xref:Microsoft.AspNetCore.Http.IFormFile> del `null`file non viene eseguito e gli eventuali argomenti associati sono .</span><span class="sxs-lookup"><span data-stu-id="f9569-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="f9569-346">Verificare inoltre che la denominazione del [caricamento nei dati del modulo corrisponda a quella dell'app.](#match-name-attribute-value-to-parameter-name-of-post-method)</span><span class="sxs-lookup"><span data-stu-id="f9569-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="f9569-347">Flusso era troppo lungo</span><span class="sxs-lookup"><span data-stu-id="f9569-347">Stream was too long</span></span>

<span data-ttu-id="f9569-348">Gli esempi in questo <xref:System.IO.MemoryStream> argomento si basano su come mantenere il contenuto del file caricato.</span><span class="sxs-lookup"><span data-stu-id="f9569-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="f9569-349">Il limite di `MemoryStream` `int.MaxValue`dimensione di a è .</span><span class="sxs-lookup"><span data-stu-id="f9569-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="f9569-350">Se lo scenario di caricamento dei file dell'app richiede contenuto di file di dimensioni `MemoryStream` superiori a 50 MB, usa un approccio alternativo che non si basi su un singolo per contenere il contenuto di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="f9569-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f9569-351">ASP.NET Core supporta il caricamento di uno o più file tramite l'associazione di modelli memorizzati nel buffer per file più piccoli e lo streaming senza buffer per file di dimensioni maggiori.</span><span class="sxs-lookup"><span data-stu-id="f9569-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="f9569-352">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9569-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f9569-353">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="f9569-353">Security considerations</span></span>

<span data-ttu-id="f9569-354">Prestare attenzione quando si fornisce agli utenti la possibilità di caricare file su un server.</span><span class="sxs-lookup"><span data-stu-id="f9569-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="f9569-355">Gli aggressori possono tentare di:</span><span class="sxs-lookup"><span data-stu-id="f9569-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="f9569-356">Eseguire attacchi [Denial of Service.](/windows-hardware/drivers/ifs/denial-of-service)</span><span class="sxs-lookup"><span data-stu-id="f9569-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="f9569-357">Caricare virus o malware.</span><span class="sxs-lookup"><span data-stu-id="f9569-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="f9569-358">Compromettere reti e server in altri modi.</span><span class="sxs-lookup"><span data-stu-id="f9569-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="f9569-359">I passaggi di sicurezza che riducono la probabilità di un attacco riuscito sono:</span><span class="sxs-lookup"><span data-stu-id="f9569-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="f9569-360">Caricare i file in un'area di caricamento file dedicata, preferibilmente in un'unità non di sistema.</span><span class="sxs-lookup"><span data-stu-id="f9569-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="f9569-361">Una posizione dedicata semplifica l'imponinza di restrizioni di sicurezza sui file caricati.</span><span class="sxs-lookup"><span data-stu-id="f9569-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="f9569-362">Disabilitare le autorizzazioni di esecuzione per il percorso di caricamento del file.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f9569-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="f9569-363">**Non** rendere persistenti i file caricati nella stessa struttura di directory dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f9569-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="f9569-364">Usa un nome di file sicuro determinato dall'app.</span><span class="sxs-lookup"><span data-stu-id="f9569-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="f9569-365">Non utilizzare un nome di file fornito dall'utente o il nome di file non attendibile del file caricato. &dagger; HTML codificare il nome del file non attendibile durante la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="f9569-366">Ad esempio, la registrazione del nome del file o la visualizzazione nell'interfaccia utente (Razor codifica automaticamente l'output).</span><span class="sxs-lookup"><span data-stu-id="f9569-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="f9569-367">Consenti solo le estensioni di file approvate per le specifiche di progettazione dell'app.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f9569-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="f9569-368">Verificare che i controlli sul lato client vengano eseguiti sul server. &dagger; I controlli lato client sono facili da aggirare.</span><span class="sxs-lookup"><span data-stu-id="f9569-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="f9569-369">Controllare le dimensioni di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="f9569-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="f9569-370">Impostare un limite di dimensione massima per impedire caricamenti di grandi dimensioni.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f9569-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="f9569-371">Quando i file non devono essere sovrascritti da un file caricato con lo stesso nome, controllare il nome del file rispetto al database o all'archiviazione fisica prima di caricare il file.</span><span class="sxs-lookup"><span data-stu-id="f9569-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="f9569-372">**Eseguire uno scanner antivirus/malware sul contenuto caricato prima che il file venga archiviato.**</span><span class="sxs-lookup"><span data-stu-id="f9569-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="f9569-373">&dagger;L'app di esempio illustra un approccio che soddisfa i criteri.</span><span class="sxs-lookup"><span data-stu-id="f9569-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="f9569-374">Il caricamento di codice dannoso in un sistema è spesso il primo passaggio per l'esecuzione di codice in grado di:</span><span class="sxs-lookup"><span data-stu-id="f9569-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="f9569-375">Ottenere completamente il controllo di un sistema.</span><span class="sxs-lookup"><span data-stu-id="f9569-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="f9569-376">Sovraccaricare un sistema con il risultato che il sistema si arresta in modo anomalo.</span><span class="sxs-lookup"><span data-stu-id="f9569-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="f9569-377">Compromettere i dati di sistemi o utenti.</span><span class="sxs-lookup"><span data-stu-id="f9569-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="f9569-378">Applicare graffiti a un'interfaccia utente pubblica.</span><span class="sxs-lookup"><span data-stu-id="f9569-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="f9569-379">Per informazioni sulla riduzione della superficie di attacco quando si accettano file dagli utenti, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="f9569-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="f9569-380">Caricamento di file senza restrizioni</span><span class="sxs-lookup"><span data-stu-id="f9569-380">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="f9569-381">Protezione di Azure: Verificare i controlli appropriati quando si accettano file dagli utenti</span><span class="sxs-lookup"><span data-stu-id="f9569-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="f9569-382">Per altre informazioni sull'implementazione di misure di sicurezza, inclusi esempi dall'app di esempio, vedere la sezione [Convalida.For](#validation) more information on implementing security measures, including examples from the sample app, see the Validation section.</span><span class="sxs-lookup"><span data-stu-id="f9569-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="f9569-383">Scenari di archiviazioneStorage scenarios</span><span class="sxs-lookup"><span data-stu-id="f9569-383">Storage scenarios</span></span>

<span data-ttu-id="f9569-384">Le opzioni di archiviazione comuni per i file includono:Common storage options for files include:</span><span class="sxs-lookup"><span data-stu-id="f9569-384">Common storage options for files include:</span></span>

* <span data-ttu-id="f9569-385">Database</span><span class="sxs-lookup"><span data-stu-id="f9569-385">Database</span></span>

  * <span data-ttu-id="f9569-386">Per i caricamenti di file di piccole dimensioni, un database è spesso più veloce delle opzioni di archiviazione fisica (file system o condivisione di rete).</span><span class="sxs-lookup"><span data-stu-id="f9569-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="f9569-387">Un database è spesso più conveniente rispetto alle opzioni di archiviazione fisica perché il recupero di un record di database per i dati utente può fornire contemporaneamente il contenuto del file (ad esempio, un'immagine avatar).</span><span class="sxs-lookup"><span data-stu-id="f9569-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="f9569-388">Un database è potenzialmente meno costoso rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="f9569-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="f9569-389">Archiviazione fisica (file system o condivisione di rete)</span><span class="sxs-lookup"><span data-stu-id="f9569-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="f9569-390">Per i caricamenti di file di grandi dimensioni:</span><span class="sxs-lookup"><span data-stu-id="f9569-390">For large file uploads:</span></span>
    * <span data-ttu-id="f9569-391">I limiti del database possono limitare le dimensioni del caricamento.</span><span class="sxs-lookup"><span data-stu-id="f9569-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="f9569-392">L'archiviazione fisica è spesso meno economica dell'archiviazione in un database.</span><span class="sxs-lookup"><span data-stu-id="f9569-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="f9569-393">L'archiviazione fisica è potenzialmente meno costosa rispetto all'utilizzo di un servizio di archiviazione dati.</span><span class="sxs-lookup"><span data-stu-id="f9569-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="f9569-394">Il processo dell'app deve disporre delle autorizzazioni di lettura e scrittura per il percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="f9569-395">**Non concedere mai l'autorizzazione di esecuzione.**</span><span class="sxs-lookup"><span data-stu-id="f9569-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="f9569-396">Servizio di archiviazione dati (ad esempio, Archiviazione BLOB di [Azure)](https://azure.microsoft.com/services/storage/blobs/)</span><span class="sxs-lookup"><span data-stu-id="f9569-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="f9569-397">I servizi offrono in genere una maggiore scalabilità e resilienza rispetto alle soluzioni locali che sono in genere soggette a singoli punti di errore.</span><span class="sxs-lookup"><span data-stu-id="f9569-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="f9569-398">I servizi sono potenzialmente più bassi in scenari di infrastruttura di archiviazione di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="f9569-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="f9569-399">Per altre informazioni, vedere [Guida introduttiva: Usare .NET per creare un BLOB nell'archivio oggetti.](/azure/storage/blobs/storage-quickstart-blobs-dotnet)</span><span class="sxs-lookup"><span data-stu-id="f9569-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="f9569-400">L'argomento <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>illustra <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> , ma può <xref:System.IO.FileStream> essere usato per <xref:System.IO.Stream>salvare un archivio BLOB quando si lavora con un oggetto .</span><span class="sxs-lookup"><span data-stu-id="f9569-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="f9569-401">Scenari di caricamento di file</span><span class="sxs-lookup"><span data-stu-id="f9569-401">File upload scenarios</span></span>

<span data-ttu-id="f9569-402">Due approcci generali per il caricamento dei file sono il buffering e lo streaming.</span><span class="sxs-lookup"><span data-stu-id="f9569-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="f9569-403">**responseBuffering**</span><span class="sxs-lookup"><span data-stu-id="f9569-403">**Buffering**</span></span>

<span data-ttu-id="f9569-404">L'intero file viene <xref:Microsoft.AspNetCore.Http.IFormFile>letto in un oggetto , che è una rappresentazione in C, del file utilizzato per elaborare o salvare il file.</span><span class="sxs-lookup"><span data-stu-id="f9569-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="f9569-405">Le risorse (disco, memoria) utilizzate dai caricamenti di file dipendono dal numero e dalle dimensioni dei caricamenti simultanei di file.</span><span class="sxs-lookup"><span data-stu-id="f9569-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="f9569-406">Se un'app tenta di memorizzare nel buffer troppi caricamenti, il sito si blocca quando si esaurisce la memoria o lo spazio su disco.</span><span class="sxs-lookup"><span data-stu-id="f9569-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="f9569-407">Se le dimensioni o la frequenza dei caricamenti di file stanno esaurendo le risorse dell'app, usa lo streaming.</span><span class="sxs-lookup"><span data-stu-id="f9569-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="f9569-408">Qualsiasi singolo file memorizzato nel buffer superiore a 64 KB viene spostato dalla memoria a un file temporaneo su disco.</span><span class="sxs-lookup"><span data-stu-id="f9569-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="f9569-409">Il buffering di file di piccole dimensioni è illustrato nelle sezioni seguenti di questo argomento:</span><span class="sxs-lookup"><span data-stu-id="f9569-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="f9569-410">Archiviazione fisica</span><span class="sxs-lookup"><span data-stu-id="f9569-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="f9569-411">Database</span><span class="sxs-lookup"><span data-stu-id="f9569-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="f9569-412">**Flusso**</span><span class="sxs-lookup"><span data-stu-id="f9569-412">**Streaming**</span></span>

<span data-ttu-id="f9569-413">Il file viene ricevuto da una richiesta multiparte ed elaborato direttamente o salvato dall'app.</span><span class="sxs-lookup"><span data-stu-id="f9569-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="f9569-414">Lo streaming non migliora in modo significativo le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="f9569-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="f9569-415">Lo streaming riduce le richieste di memoria o spazio su disco durante il caricamento dei file.</span><span class="sxs-lookup"><span data-stu-id="f9569-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="f9569-416">Lo streaming di file di grandi dimensioni è illustrato nella sezione Caricare file di [grandi dimensioni con lo streaming.](#upload-large-files-with-streaming)</span><span class="sxs-lookup"><span data-stu-id="f9569-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="f9569-417">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer nell'archiviazione fisicaUpload small files with buffered model binding to physical storage</span><span class="sxs-lookup"><span data-stu-id="f9569-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="f9569-418">Per caricare file di piccole dimensioni, utilizzare un modulo multiparte o costruire una richiesta POST utilizzando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f9569-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="f9569-419">L'esempio seguente illustra l'uso di un modulo Pagine Razor per caricare un singolo file (*Pages/BufferedSingleFileUploadPhysical.cshtml* nell'app di esempio):</span><span class="sxs-lookup"><span data-stu-id="f9569-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="f9569-420">L'esempio seguente è analogo all'esempio precedente, ad eccezione del fatto che:</span><span class="sxs-lookup"><span data-stu-id="f9569-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="f9569-421">JavaScript ([API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API)) viene utilizzato per inviare i dati del modulo.</span><span class="sxs-lookup"><span data-stu-id="f9569-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="f9569-422">Non c'è convalida.</span><span class="sxs-lookup"><span data-stu-id="f9569-422">There's no validation.</span></span>

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

<span data-ttu-id="f9569-423">Per eseguire il modulo POST in JavaScript per i client che [non supportano l'API Fetch](https://caniuse.com/#feat=fetch), utilizzare uno dei seguenti approcci:</span><span class="sxs-lookup"><span data-stu-id="f9569-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="f9569-424">Utilizzare un polyfill Fetch (ad esempio, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="f9569-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="f9569-425">Usare `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="f9569-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="f9569-426">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f9569-426">For example:</span></span>

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

<span data-ttu-id="f9569-427">Per supportare i caricamenti di file, i`enctype`moduli `multipart/form-data`HTML devono specificare un tipo di codifica ( ) di .</span><span class="sxs-lookup"><span data-stu-id="f9569-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="f9569-428">Per `files` un elemento di input per `multiple` supportare `<input>` il caricamento di più file forniscono l'attributo sull'elemento:For a input element to support uploading multiple files provide the attribute on the element:</span><span class="sxs-lookup"><span data-stu-id="f9569-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="f9569-429">È possibile accedere ai singoli file caricati <xref:Microsoft.AspNetCore.Http.IFormFile>sul server tramite [L'associazione di](xref:mvc/models/model-binding) modelli utilizzando .</span><span class="sxs-lookup"><span data-stu-id="f9569-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="f9569-430">L'app di esempio illustra più caricamenti di file con buffer per scenari di archiviazione fisica e di database.</span><span class="sxs-lookup"><span data-stu-id="f9569-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="f9569-431">**Non** utilizzare `FileName` la <xref:Microsoft.AspNetCore.Http.IFormFile> proprietà other than per la visualizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="f9569-432">Durante la visualizzazione o la registrazione, IL codice HTML codifica il nome del file.</span><span class="sxs-lookup"><span data-stu-id="f9569-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="f9569-433">Un utente malintenzionato può fornire un nome file dannoso, inclusi i percorsi completi o relativi.</span><span class="sxs-lookup"><span data-stu-id="f9569-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="f9569-434">Le domande devono:</span><span class="sxs-lookup"><span data-stu-id="f9569-434">Applications should:</span></span>
>
> * <span data-ttu-id="f9569-435">Rimuovere il percorso dal nome file fornito dall'utente.</span><span class="sxs-lookup"><span data-stu-id="f9569-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="f9569-436">Salvare il nome del file con codifica HTML rimosso e rimosso dal percorso per l'interfaccia utente o la registrazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="f9569-437">Generare un nuovo nome file casuale per l'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="f9569-438">Il codice seguente rimuove il percorso dal nome del file:</span><span class="sxs-lookup"><span data-stu-id="f9569-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="f9569-439">Gli esempi forniti finora non tengono conto delle considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="f9569-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="f9569-440">Ulteriori informazioni vengono fornite dalle sezioni seguenti e [dall'app di esempio:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)</span><span class="sxs-lookup"><span data-stu-id="f9569-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f9569-441">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="f9569-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f9569-442">Validation</span><span class="sxs-lookup"><span data-stu-id="f9569-442">Validation</span></span>](#validation)

<span data-ttu-id="f9569-443">Quando si caricano file <xref:Microsoft.AspNetCore.Http.IFormFile>utilizzando l'associazione di modelli e , il metodo di azione può accettare:</span><span class="sxs-lookup"><span data-stu-id="f9569-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="f9569-444">Un <xref:Microsoft.AspNetCore.Http.IFormFile>singolo file .</span><span class="sxs-lookup"><span data-stu-id="f9569-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="f9569-445">Una delle seguenti raccolte che rappresentano diversi file:</span><span class="sxs-lookup"><span data-stu-id="f9569-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="f9569-446">[Elenco](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="f9569-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="f9569-447">L'associazione corrisponde ai file del modulo in base al nome.</span><span class="sxs-lookup"><span data-stu-id="f9569-447">Binding matches form files by name.</span></span> <span data-ttu-id="f9569-448">Ad esempio, `name` il `<input type="file" name="formFile">` valore HTML in deve corrispondere`FormFile`al parametro/proprietà di C, associato a ( ).</span><span class="sxs-lookup"><span data-stu-id="f9569-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="f9569-449">Per altre informazioni, vedere la sezione Corrispondenza valore [dell'attributo Nome con nome del metodo POST.](#match-name-attribute-value-to-parameter-name-of-post-method)</span><span class="sxs-lookup"><span data-stu-id="f9569-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="f9569-450">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="f9569-450">The following example:</span></span>

* <span data-ttu-id="f9569-451">Scorre in ciclo uno o più file caricati.</span><span class="sxs-lookup"><span data-stu-id="f9569-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="f9569-452">Utilizza [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) per restituire un percorso completo per un file, incluso il nome del file.</span><span class="sxs-lookup"><span data-stu-id="f9569-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="f9569-453">Salva i file nel file system locale utilizzando un nome di file generato dall'app.</span><span class="sxs-lookup"><span data-stu-id="f9569-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="f9569-454">Restituisce il numero totale e le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="f9569-454">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="f9569-455">Utilizzare `Path.GetRandomFileName` per generare un nome di file senza un percorso.</span><span class="sxs-lookup"><span data-stu-id="f9569-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="f9569-456">Nell'esempio seguente, il percorso viene ottenuto dalla configurazione:In the following example, the path is obtained from configuration:</span><span class="sxs-lookup"><span data-stu-id="f9569-456">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="f9569-457">Il percorso passato <xref:System.IO.FileStream> all'oggetto *deve* includere il nome del file.</span><span class="sxs-lookup"><span data-stu-id="f9569-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="f9569-458">Se il nome del file <xref:System.UnauthorizedAccessException> non viene fornito, viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="f9569-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="f9569-459">I file caricati utilizzando la <xref:Microsoft.AspNetCore.Http.IFormFile> tecnica vengono memorizzati nel buffer in memoria o su disco sul server prima dell'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="f9569-460">All'interno del <xref:Microsoft.AspNetCore.Http.IFormFile> metodo di azione, il contenuto è accessibile come <xref:System.IO.Stream>oggetto .</span><span class="sxs-lookup"><span data-stu-id="f9569-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="f9569-461">Oltre al file system locale, i file possono essere salvati in una condivisione di rete o in un servizio di archiviazione file, ad esempio [Archiviazione BLOB](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)di Azure .</span><span class="sxs-lookup"><span data-stu-id="f9569-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="f9569-462">Per un altro esempio che esegue un ciclo su più file per il caricamento e usa nomi di file sicuri, vedere *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="f9569-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="f9569-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) genera <xref:System.IO.IOException> un'eccezione se vengono creati più di 65.535 file senza eliminare i file temporanei precedenti.</span><span class="sxs-lookup"><span data-stu-id="f9569-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="f9569-464">Il limite di 65.535 file è un limite per server.</span><span class="sxs-lookup"><span data-stu-id="f9569-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="f9569-465">Per ulteriori informazioni su questo limite nel sistema operativo Windows, vedere le osservazioni nei seguenti argomenti:</span><span class="sxs-lookup"><span data-stu-id="f9569-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="f9569-466">GetTempFileNameA (funzione)</span><span class="sxs-lookup"><span data-stu-id="f9569-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="f9569-467">Caricare file di piccole dimensioni con associazione di modelli memorizzati nel buffer in un databaseUpload small files with buffered model binding to a database</span><span class="sxs-lookup"><span data-stu-id="f9569-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="f9569-468">Per archiviare i dati di file binari <xref:System.Byte> in un database utilizzando Entity [Framework](/ef/core/index), definire una proprietà di matrice nell'entità:</span><span class="sxs-lookup"><span data-stu-id="f9569-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="f9569-469">Specificare una proprietà del modello <xref:Microsoft.AspNetCore.Http.IFormFile>di pagina per la classe che include un:</span><span class="sxs-lookup"><span data-stu-id="f9569-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="f9569-470"><xref:Microsoft.AspNetCore.Http.IFormFile>può essere utilizzato direttamente come parametro del metodo di azione o come proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="f9569-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="f9569-471">Nell'esempio precedente viene utilizzata una proprietà del modello associato.</span><span class="sxs-lookup"><span data-stu-id="f9569-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="f9569-472">Viene `FileUpload` utilizzato nel modulo Pagine razor:</span><span class="sxs-lookup"><span data-stu-id="f9569-472">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="f9569-473">Quando il modulo è POSTed al <xref:Microsoft.AspNetCore.Http.IFormFile> server, copiare il in un flusso e salvarlo come matrice di byte nel database.</span><span class="sxs-lookup"><span data-stu-id="f9569-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="f9569-474">Nell'esempio seguente `_dbContext` viene archiviato il contesto di database dell'app:In the following example, stores the app's database context:</span><span class="sxs-lookup"><span data-stu-id="f9569-474">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="f9569-475">L'esempio precedente è simile a uno scenario illustrato nell'app di esempio:The preceding example is similar to a scenario demonstrated in the sample app:</span><span class="sxs-lookup"><span data-stu-id="f9569-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="f9569-476">*Pagine/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f9569-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="f9569-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f9569-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="f9569-478">Quando si archiviano dati binari all'interno di database relazionali, è necessario prestare attenzione, perché l'operazione può influire negativamente sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="f9569-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="f9569-479">Non fare affidamento o `FileName` considerare <xref:Microsoft.AspNetCore.Http.IFormFile> attendibile la proprietà di senza convalida.</span><span class="sxs-lookup"><span data-stu-id="f9569-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f9569-480">La `FileName` proprietà deve essere utilizzata solo per scopi di visualizzazione e solo dopo la codifica HTML.</span><span class="sxs-lookup"><span data-stu-id="f9569-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="f9569-481">Gli esempi forniti non tengono conto delle considerazioni sulla sicurezza.</span><span class="sxs-lookup"><span data-stu-id="f9569-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="f9569-482">Ulteriori informazioni vengono fornite dalle sezioni seguenti e [dall'app di esempio:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)</span><span class="sxs-lookup"><span data-stu-id="f9569-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f9569-483">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="f9569-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f9569-484">Validation</span><span class="sxs-lookup"><span data-stu-id="f9569-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="f9569-485">Caricare file di grandi dimensioni con lo streaming</span><span class="sxs-lookup"><span data-stu-id="f9569-485">Upload large files with streaming</span></span>

<span data-ttu-id="f9569-486">Nell'esempio seguente viene illustrato come utilizzare JavaScript per trasmettere un file a un'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="f9569-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="f9569-487">Il token antifalandria del file viene generato utilizzando un attributo di filtro personalizzato e passato alle intestazioni HTTP del client anziché nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="f9569-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="f9569-488">Poiché il metodo di azione elabora direttamente i dati caricati, l'associazione del modello di modulo è disabilitata da un altro filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="f9569-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="f9569-489">All'interno dell'azione, il contenuto del form viene letto tramite un `MultipartReader`, che legge ogni singola `MultipartSection`, elaborando il file o archiviandone il contenuto, come appropriato.</span><span class="sxs-lookup"><span data-stu-id="f9569-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="f9569-490">Dopo la lettura delle sezioni multiparte, l'azione esegue la propria associazione di modello.</span><span class="sxs-lookup"><span data-stu-id="f9569-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="f9569-491">La risposta iniziale della pagina carica il modulo e salva `GenerateAntiforgeryTokenCookieAttribute` un token antifalsificazione in un cookie (tramite l'attributo).</span><span class="sxs-lookup"><span data-stu-id="f9569-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="f9569-492">L'attributo utilizza ASP.NET [supporto antifasciacquario](xref:security/anti-request-forgery) incorporato di Core per impostare un cookie con un token di richiesta:The attribute uses ASP.NET Core's built-in antiforgery support to set a cookie with a request token:</span><span class="sxs-lookup"><span data-stu-id="f9569-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="f9569-493">L'oggetto viene utilizzato per disabilitare l'associazione di modelli:The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span><span class="sxs-lookup"><span data-stu-id="f9569-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="f9569-494">Nell'app di `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` esempio e vengono applicati come `/StreamedSingleFileUploadDb` filtri `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` ai modelli di applicazione pagina di e tramite [le convenzioni Razor Pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="f9569-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="f9569-495">Poiché l'associazione di modelli non legge il form, i parametri associati dal form non vengono associati (query, route e intestazione continuano a funzionare).</span><span class="sxs-lookup"><span data-stu-id="f9569-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="f9569-496">Il metodo di azione `Request` funziona direttamente con la proprietà.</span><span class="sxs-lookup"><span data-stu-id="f9569-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="f9569-497">Per leggere ogni sezione, viene usato un `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="f9569-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="f9569-498">I dati chiave/valore `KeyValueAccumulator`vengono memorizzati in un file .</span><span class="sxs-lookup"><span data-stu-id="f9569-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="f9569-499">Dopo la lettura delle sezioni multiparte, il contenuto di `KeyValueAccumulator` vengono utilizzati per associare i dati del form a un tipo di modello.</span><span class="sxs-lookup"><span data-stu-id="f9569-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="f9569-500">Il `StreamingController.UploadDatabase` metodo completo per lo streaming in un database con EF Core:The complete method for streaming to a database with EF Core:</span><span class="sxs-lookup"><span data-stu-id="f9569-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="f9569-501">`MultipartRequestHelper`(*Utilità/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="f9569-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="f9569-502">Il `StreamingController.UploadPhysical` metodo completo per lo streaming in una posizione fisica:</span><span class="sxs-lookup"><span data-stu-id="f9569-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="f9569-503">Nell'app di esempio, i `FileHelpers.ProcessStreamedFile`controlli di convalida vengono gestiti da .</span><span class="sxs-lookup"><span data-stu-id="f9569-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="f9569-504">Convalida</span><span class="sxs-lookup"><span data-stu-id="f9569-504">Validation</span></span>

<span data-ttu-id="f9569-505">La classe dell'app di `FileHelpers` esempio illustra <xref:Microsoft.AspNetCore.Http.IFormFile> diversi controlli per i caricamenti di file memorizzati nel buffer e trasmessi tè.</span><span class="sxs-lookup"><span data-stu-id="f9569-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="f9569-506">Per <xref:Microsoft.AspNetCore.Http.IFormFile> elaborare i caricamenti di file `ProcessFormFile` memorizzati nel buffer nell'app di esempio, vedere il metodo nel file *Utilities/FileHelpers.cs.For* processing buffered file uploads in the sample app, see the method in the Utilities/FileHelpers.cs file.</span><span class="sxs-lookup"><span data-stu-id="f9569-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="f9569-507">Per l'elaborazione di `ProcessStreamedFile` file trasmessi, vedere il metodo nello stesso file.</span><span class="sxs-lookup"><span data-stu-id="f9569-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="f9569-508">I metodi di elaborazione della convalida illustrati nell'app di esempio non analizzano il contenuto dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="f9569-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="f9569-509">Nella maggior parte degli scenari di produzione, un'API dello scanner antivirus/malware viene utilizzata nel file prima di rendere il file disponibile agli utenti o ad altri sistemi.</span><span class="sxs-lookup"><span data-stu-id="f9569-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="f9569-510">Anche se l'esempio di argomento fornisce un esempio `FileHelpers` funzionante di tecniche di convalida, non implementare la classe in un'app di produzione a meno che non si:Although the topic sample provides a working example of validation techniques, don't implement the class in a production app unless you:</span><span class="sxs-lookup"><span data-stu-id="f9569-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="f9569-511">Comprendere appieno l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="f9569-512">Modificare l'implementazione in base all'ambiente e alle specifiche dell'app.</span><span class="sxs-lookup"><span data-stu-id="f9569-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="f9569-513">**Non implementare mai indiscriminatamente il codice di sicurezza in un'app senza soddisfare questi requisiti.**</span><span class="sxs-lookup"><span data-stu-id="f9569-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="f9569-514">Convalida contenuto</span><span class="sxs-lookup"><span data-stu-id="f9569-514">Content validation</span></span>

<span data-ttu-id="f9569-515">**Utilizzare un'API di scansione antivirus/malware di terze parti sul contenuto caricato.**</span><span class="sxs-lookup"><span data-stu-id="f9569-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="f9569-516">L'analisi dei file richiede risorse del server in scenari con volumi elevati.</span><span class="sxs-lookup"><span data-stu-id="f9569-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="f9569-517">Se le prestazioni di elaborazione delle richieste diminuiscono a causa dell'analisi dei file, è consigliabile scaricare il lavoro di scansione in un [servizio in background,](xref:fundamentals/host/hosted-services)possibilmente un servizio in esecuzione su un server diverso dal server dell'app.</span><span class="sxs-lookup"><span data-stu-id="f9569-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="f9569-518">In genere, i file caricati vengono mantenuti in un'area in quarantena fino a quando non vengono esaminati dall'utilità di analisi antivirus in background.</span><span class="sxs-lookup"><span data-stu-id="f9569-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="f9569-519">Quando un file passa, il file viene spostato nel percorso di archiviazione del file normale.</span><span class="sxs-lookup"><span data-stu-id="f9569-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="f9569-520">Questi passaggi vengono in genere eseguiti insieme a un record di database che indica lo stato di analisi di un file.</span><span class="sxs-lookup"><span data-stu-id="f9569-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="f9569-521">Utilizzando tale approccio, l'app e il server applicazioni rimangono concentrati sulla risposta alle richieste.</span><span class="sxs-lookup"><span data-stu-id="f9569-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="f9569-522">Convalida dell'estensione file</span><span class="sxs-lookup"><span data-stu-id="f9569-522">File extension validation</span></span>

<span data-ttu-id="f9569-523">L'estensione del file caricato deve essere confrontata con un elenco di estensioni consentite.</span><span class="sxs-lookup"><span data-stu-id="f9569-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="f9569-524">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f9569-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="f9569-525">Convalida della firma del file</span><span class="sxs-lookup"><span data-stu-id="f9569-525">File signature validation</span></span>

<span data-ttu-id="f9569-526">La firma di un file è determinata dai primi byte all'inizio di un file.</span><span class="sxs-lookup"><span data-stu-id="f9569-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="f9569-527">Questi byte possono essere utilizzati per indicare se l'estensione corrisponde al contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="f9569-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="f9569-528">L'app di esempio controlla le firme dei file per alcuni tipi di file comuni.</span><span class="sxs-lookup"><span data-stu-id="f9569-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="f9569-529">Nell'esempio seguente, la firma del file per un'immagine JPEG viene confrontata con il file:</span><span class="sxs-lookup"><span data-stu-id="f9569-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="f9569-530">Per ottenere ulteriori firme di file, vedere il [Database delle firme](https://www.filesignatures.net/) dei file e le specifiche ufficiali dei file.</span><span class="sxs-lookup"><span data-stu-id="f9569-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="f9569-531">Sicurezza del nome file</span><span class="sxs-lookup"><span data-stu-id="f9569-531">File name security</span></span>

<span data-ttu-id="f9569-532">Non utilizzare mai un nome di file fornito dal client per salvare un file nell'archivio fisico.</span><span class="sxs-lookup"><span data-stu-id="f9569-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="f9569-533">Creare un nome di file sicuro per il file utilizzando [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) per creare un percorso completo (incluso il nome file) per l'archiviazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="f9569-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="f9569-534">Razor codifica automaticamente i valori delle proprietà per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="f9569-534">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="f9569-535">Il codice seguente è sicuro da usare:The following code is safe to use:</span><span class="sxs-lookup"><span data-stu-id="f9569-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="f9569-536">Al di fuori <xref:System.Net.WebUtility.HtmlEncode*> di Razor, il contenuto del nome file da una richiesta dell'utente.</span><span class="sxs-lookup"><span data-stu-id="f9569-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="f9569-537">Molte implementazioni devono includere un controllo che il file esista; in caso contrario, il file viene sovrascritto da un file con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="f9569-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="f9569-538">Fornisci logica aggiuntiva per soddisfare le specifiche della tua app.</span><span class="sxs-lookup"><span data-stu-id="f9569-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="f9569-539">Convalida delle dimensioni</span><span class="sxs-lookup"><span data-stu-id="f9569-539">Size validation</span></span>

<span data-ttu-id="f9569-540">Limitare le dimensioni dei file caricati.</span><span class="sxs-lookup"><span data-stu-id="f9569-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="f9569-541">Nell'app di esempio, la dimensione del file è limitata a 2 MB (indicata in byte).</span><span class="sxs-lookup"><span data-stu-id="f9569-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="f9569-542">Il limite viene fornito tramite [Configuration](xref:fundamentals/configuration/index) dal file *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="f9569-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="f9569-543">Il `FileSizeLimit` viene iniettato nelle `PageModel` classi:</span><span class="sxs-lookup"><span data-stu-id="f9569-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="f9569-544">Quando una dimensione del file supera il limite, il file viene rifiutato:</span><span class="sxs-lookup"><span data-stu-id="f9569-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="f9569-545">Corrispondenza valore attributo nome al nome del metodo POST</span><span class="sxs-lookup"><span data-stu-id="f9569-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="f9569-546">Nei moduli non Razor che formano dati POST `FormData` o utilizzano direttamente JavaScript, `FormData` il nome specificato nell'elemento del modulo o deve corrispondere al nome del parametro nell'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="f9569-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="f9569-547">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="f9569-547">In the following example:</span></span>

* <span data-ttu-id="f9569-548">Quando si `<input>` utilizza `name` un elemento, l'attributo viene impostato sul valore `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="f9569-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="f9569-549">Quando `FormData` si utilizza in JavaScript, il `battlePlans`nome è impostato sul valore :</span><span class="sxs-lookup"><span data-stu-id="f9569-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="f9569-550">Utilizzare un nome corrispondente per il parametro del metodo C , (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="f9569-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="f9569-551">Per un metodo del gestore di pagina Razor Pages denominato `Upload`:</span><span class="sxs-lookup"><span data-stu-id="f9569-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="f9569-552">Per un metodo di azione del controller MVC POST:For an MVC POST controller action method:</span><span class="sxs-lookup"><span data-stu-id="f9569-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="f9569-553">Configurazione di server e app</span><span class="sxs-lookup"><span data-stu-id="f9569-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="f9569-554">Limite di lunghezza del corpo multiparte</span><span class="sxs-lookup"><span data-stu-id="f9569-554">Multipart body length limit</span></span>

<span data-ttu-id="f9569-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>imposta il limite per la lunghezza di ogni corpo multiparte.</span><span class="sxs-lookup"><span data-stu-id="f9569-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="f9569-556">Le sezioni del modulo <xref:System.IO.InvalidDataException> che superano questo limite generano un errore quando vengono analizzate.</span><span class="sxs-lookup"><span data-stu-id="f9569-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="f9569-557">Il valore predefinito è 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="f9569-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="f9569-558">Personalizzare il limite <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> utilizzando `Startup.ConfigureServices`l'impostazione in:</span><span class="sxs-lookup"><span data-stu-id="f9569-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f9569-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>viene utilizzato per <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> impostare l'oggetto per una singola pagina o azione.</span><span class="sxs-lookup"><span data-stu-id="f9569-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="f9569-560">In un'app Razor Pages, applica `Startup.ConfigureServices`il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in:</span><span class="sxs-lookup"><span data-stu-id="f9569-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f9569-561">In un'app Razor Pages o in un'app MVC applicare il filtro al modello di pagina o al metodo di azione:In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span><span class="sxs-lookup"><span data-stu-id="f9569-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="f9569-562">Dimensione massima del corpo della richiesta del gheccello</span><span class="sxs-lookup"><span data-stu-id="f9569-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="f9569-563">Per le app ospitate da Kestrel, la dimensione massima predefinita del corpo della richiesta è 30.000.000 byte, ovvero circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="f9569-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f9569-564">Personalizzare il limite utilizzando l'opzione server [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="f9569-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="f9569-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>viene utilizzato per impostare il [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) per una singola pagina o azione.</span><span class="sxs-lookup"><span data-stu-id="f9569-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="f9569-566">In un'app Razor Pages, applica `Startup.ConfigureServices`il filtro con una [convenzione](xref:razor-pages/razor-pages-conventions) in:</span><span class="sxs-lookup"><span data-stu-id="f9569-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f9569-567">In un'app di pagine Razor o in un'app MVC, applica il filtro alla classe del gestore di pagina o al metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="f9569-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="f9569-568">Altri limiti di Kestrel</span><span class="sxs-lookup"><span data-stu-id="f9569-568">Other Kestrel limits</span></span>

<span data-ttu-id="f9569-569">Altri limiti di Kestrel possono richiedere le app ospitate da Kestrel:</span><span class="sxs-lookup"><span data-stu-id="f9569-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="f9569-570">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="f9569-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="f9569-571">Frequenza dei dati di richiesta e risposta</span><span class="sxs-lookup"><span data-stu-id="f9569-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="f9569-572">Limite di lunghezza del contenuto IIS</span><span class="sxs-lookup"><span data-stu-id="f9569-572">IIS content length limit</span></span>

<span data-ttu-id="f9569-573">Il limite di`maxAllowedContentLength`richieste predefinito ( ) è 30.000.000 byte, ovvero circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="f9569-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="f9569-574">Personalizzare il limite nel file *web.config:*</span><span class="sxs-lookup"><span data-stu-id="f9569-574">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="f9569-575">Questa impostazione si applica solo a IIS.</span><span class="sxs-lookup"><span data-stu-id="f9569-575">This setting only applies to IIS.</span></span> <span data-ttu-id="f9569-576">Il comportamento non si verifica per impostazione predefinita con l'hosting in Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f9569-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="f9569-577">Per ulteriori informazioni, vedere [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="f9569-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="f9569-578">Limitazioni nel ASP.NET Core Module o la presenza del modulo filtro richieste IIS può limitare i caricamenti a 2 o 4 GB.</span><span class="sxs-lookup"><span data-stu-id="f9569-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="f9569-579">Per ulteriori informazioni, vedere [Impossibile caricare file di dimensioni superiori a 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="f9569-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f9569-580">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="f9569-580">Troubleshoot</span></span>

<span data-ttu-id="f9569-581">Di seguito sono trattati alcuni problemi comuni riscontrati durante il caricamento di file, con le soluzioni possibili corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="f9569-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="f9569-582">Errore Non trovato quando viene distribuito in un server IIS</span><span class="sxs-lookup"><span data-stu-id="f9569-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="f9569-583">Il seguente errore indica che il file caricato supera la lunghezza del contenuto configurato del server:</span><span class="sxs-lookup"><span data-stu-id="f9569-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="f9569-584">Per ulteriori informazioni sull'aumento del limite, vedere la sezione Limite di [lunghezza del contenuto di IIS.](#iis-content-length-limit)</span><span class="sxs-lookup"><span data-stu-id="f9569-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="f9569-585">Errore di connessione</span><span class="sxs-lookup"><span data-stu-id="f9569-585">Connection failure</span></span>

<span data-ttu-id="f9569-586">Un errore di connessione e una connessione al server di reimpostazione indicaprobabilmente che il file caricato supera la dimensione massima del corpo della richiesta di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f9569-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="f9569-587">Per altre informazioni, vedere la sezione [Dimensioni massime del corpo della richiesta di Kestrel.For](#kestrel-maximum-request-body-size) more information, see the Kestrel maximum request body size section.</span><span class="sxs-lookup"><span data-stu-id="f9569-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="f9569-588">Anche i limiti di connessione del client Kestrel possono richiedere modifiche.</span><span class="sxs-lookup"><span data-stu-id="f9569-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="f9569-589">Eccezione per riferimento Null con IFormFile</span><span class="sxs-lookup"><span data-stu-id="f9569-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="f9569-590">Se il controller accetta i <xref:Microsoft.AspNetCore.Http.IFormFile> file caricati utilizzando ma il valore `null` `enctype` è `multipart/form-data`, verificare che il modulo HTML specifichi un valore di .</span><span class="sxs-lookup"><span data-stu-id="f9569-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="f9569-591">Se questo attributo non `<form>` è impostato sull'elemento, il caricamento <xref:Microsoft.AspNetCore.Http.IFormFile> del `null`file non viene eseguito e gli eventuali argomenti associati sono .</span><span class="sxs-lookup"><span data-stu-id="f9569-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="f9569-592">Verificare inoltre che la denominazione del [caricamento nei dati del modulo corrisponda a quella dell'app.](#match-name-attribute-value-to-parameter-name-of-post-method)</span><span class="sxs-lookup"><span data-stu-id="f9569-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="f9569-593">Flusso era troppo lungo</span><span class="sxs-lookup"><span data-stu-id="f9569-593">Stream was too long</span></span>

<span data-ttu-id="f9569-594">Gli esempi in questo <xref:System.IO.MemoryStream> argomento si basano su come mantenere il contenuto del file caricato.</span><span class="sxs-lookup"><span data-stu-id="f9569-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="f9569-595">Il limite di `MemoryStream` `int.MaxValue`dimensione di a è .</span><span class="sxs-lookup"><span data-stu-id="f9569-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="f9569-596">Se lo scenario di caricamento dei file dell'app richiede contenuto di file di dimensioni `MemoryStream` superiori a 50 MB, usa un approccio alternativo che non si basi su un singolo per contenere il contenuto di un file caricato.</span><span class="sxs-lookup"><span data-stu-id="f9569-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="f9569-597">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f9569-597">Additional resources</span></span>

* [<span data-ttu-id="f9569-598">Caricamento di file senza restrizioni</span><span class="sxs-lookup"><span data-stu-id="f9569-598">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="f9569-599">Sicurezza di Azure: Frame di sicurezza: Convalida dell'input Mitigazioni</span><span class="sxs-lookup"><span data-stu-id="f9569-599">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="f9569-600">Modelli di progettazione cloud di Azure: modello di chiave valetAzure Cloud Design Patterns: Valet Key pattern</span><span class="sxs-lookup"><span data-stu-id="f9569-600">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
