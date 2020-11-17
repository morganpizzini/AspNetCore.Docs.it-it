---
title: BlazorCaricamenti di file ASP.NET Core
author: guardrex
description: Informazioni su come caricare file in Blazor con il componente inputfile.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
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
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: ca49564136e030fdaf86eefac56146fcb79f7bad
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673952"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="ebcdf-103">BlazorCaricamenti di file ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebcdf-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="ebcdf-104">Di [Daniel Roth](https://github.com/danroth27) [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="ebcdf-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="ebcdf-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ebcdf-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ebcdf-106">Usare il `InputFile` componente per leggere i dati dei file del browser nel codice .NET, anche per i caricamenti di file.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="ebcdf-107">Seguire sempre le procedure consigliate per la sicurezza di caricamento file.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="ebcdf-108">Per altre informazioni, vedere <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="ebcdf-109">Componente `InputFile`</span><span class="sxs-lookup"><span data-stu-id="ebcdf-109">`InputFile` component</span></span>

<span data-ttu-id="ebcdf-110">Il componente viene sottoposta `InputFile` a rendering come input HTML di tipo `file` .</span><span class="sxs-lookup"><span data-stu-id="ebcdf-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="ebcdf-111">Per impostazione predefinita, l'utente seleziona singoli file.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-111">By default, the user selects single files.</span></span> <span data-ttu-id="ebcdf-112">Aggiungere l' `multiple` attributo per consentire all'utente di caricare più file contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="ebcdf-113">Quando uno o più file vengono selezionati dall'utente, il `InputFile` componente genera un `OnChange` evento e passa un oggetto `InputFileChangeEventArgs` che fornisce l'accesso all'elenco di file selezionati e informazioni dettagliate su ogni file.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="ebcdf-114">Per leggere i dati da un file selezionato dall'utente:</span><span class="sxs-lookup"><span data-stu-id="ebcdf-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="ebcdf-115">Chiamare `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` sul file e leggere dal flusso restituito.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-115">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="ebcdf-116">Per ulteriori informazioni, vedere la sezione [flussi di file](#file-streams) .</span><span class="sxs-lookup"><span data-stu-id="ebcdf-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="ebcdf-117">L'oggetto <xref:System.IO.Stream> restituito da `OpenReadStream` impone una dimensione massima in byte dell'oggetto `Stream` letto.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-117">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="ebcdf-118">Per impostazione predefinita, le dimensioni dei file non superiori a 512.000 byte (500 KB) possono essere lette prima che altre letture provochino un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-118">By default, files no larger than 512,000 bytes (500 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="ebcdf-119">Questo limite è presente per impedire agli sviluppatori di leggere accidentalmente file di grandi dimensioni in memoria.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="ebcdf-120">Il `maxAllowedSize` parametro in `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` può essere utilizzato per specificare una dimensione maggiore, se necessario.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-120">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="ebcdf-121">Evitare di leggere il flusso di file in ingresso direttamente in memoria.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="ebcdf-122">Ad esempio, non copiare i byte di file in un oggetto <xref:System.IO.MemoryStream> o leggere come matrice di byte.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="ebcdf-123">Questi approcci possono causare problemi di prestazioni e sicurezza, soprattutto in Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="ebcdf-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="ebcdf-124">In alternativa, è consigliabile copiare i byte dei file in un archivio esterno, ad esempio un BLOB o un file su disco.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="ebcdf-125">Un componente che riceve un file di immagine può chiamare il `RequestImageFileAsync` metodo pratico sul file per ridimensionare i dati dell'immagine nel runtime JavaScript del browser prima che l'immagine venga trasmessa nell'app.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="ebcdf-126">Nell'esempio seguente viene illustrato il caricamento di più file di immagine in un componente.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="ebcdf-127">`InputFileChangeEventArgs.GetMultipleFiles` consente la lettura di più file.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="ebcdf-128">Specificare il numero massimo di file che si prevede di leggere per impedire a un utente malintenzionato di caricare un numero maggiore di file rispetto a quanto previsto dall'app.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="ebcdf-129">`InputFileChangeEventArgs.File` consente di leggere il primo e l'unico file se il caricamento del file non supporta più file.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

<span data-ttu-id="ebcdf-130">`IBrowserFile` restituisce [i metadati esposti dal browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) come proprietà.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="ebcdf-131">Questi metadati possono essere utili per la convalida preliminare.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="ebcdf-132">Vedere ad esempio i [ `FileUpload.razor` componenti di `FilePreview.razor` esempio e](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="ebcdf-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="ebcdf-133">Flussi di file</span><span class="sxs-lookup"><span data-stu-id="ebcdf-133">File streams</span></span>

<span data-ttu-id="ebcdf-134">In un' Blazor WebAssembly applicazione, i dati vengono trasmessi direttamente nel codice .NET all'interno del browser.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-134">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="ebcdf-135">In un' Blazor Server app, i dati dei file vengono trasmessi attraverso la SignalR connessione nel codice .NET sul server mentre il file viene letto dal flusso.</span><span class="sxs-lookup"><span data-stu-id="ebcdf-135">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="ebcdf-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) consente di configurare le caratteristiche di caricamento dei file per Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="ebcdf-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ebcdf-137">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="ebcdf-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
