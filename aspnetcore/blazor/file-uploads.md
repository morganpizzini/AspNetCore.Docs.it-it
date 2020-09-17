---
title: BlazorCaricamenti di file ASP.NET Core
author: guardrex
description: Informazioni su come caricare file in Blazor con il componente inputfile.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/file-uploads
ms.openlocfilehash: de4654f2efc401143e066628b096052efa65d7a0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722981"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="ad184-103">BlazorCaricamenti di file ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ad184-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="ad184-104">Di [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ad184-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ad184-105">Usare il `InputFile` componente per leggere i dati dei file del browser nel codice .NET, anche per i caricamenti di file.</span><span class="sxs-lookup"><span data-stu-id="ad184-105">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span> <span data-ttu-id="ad184-106">Il componente viene sottoposta `InputFile` a rendering come input HTML di tipo `file` .</span><span class="sxs-lookup"><span data-stu-id="ad184-106">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="ad184-107">Per impostazione predefinita, l'utente seleziona singoli file.</span><span class="sxs-lookup"><span data-stu-id="ad184-107">By default, the user selects single files.</span></span> <span data-ttu-id="ad184-108">Aggiungere l' `multiple` attributo per consentire all'utente di caricare più file contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="ad184-108">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="ad184-109">Quando uno o più file vengono selezionati dall'utente, il `InputFile` componente genera un `OnChange` evento e passa un oggetto `InputFileChangeEventArgs` che fornisce l'accesso all'elenco di file selezionati e informazioni dettagliate su ogni file.</span><span class="sxs-lookup"><span data-stu-id="ad184-109">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="ad184-110">Un componente che riceve un file di immagine può chiamare il `RequestImageFileAsync` metodo pratico sul file per ridimensionare i dati dell'immagine nel runtime JavaScript del browser prima che l'immagine venga trasmessa nell'app.</span><span class="sxs-lookup"><span data-stu-id="ad184-110">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="ad184-111">Nell'esempio seguente viene illustrato il caricamento di più file di immagine in un componente:</span><span class="sxs-lookup"><span data-stu-id="ad184-111">The following example demonstrates multiple image file upload in a component:</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h3>Images</h3>

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
        var imageFiles = e.GetMultipleFiles();
        var format = "image/png";

        foreach (var imageFile in imageFiles)
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

<span data-ttu-id="ad184-112">Per leggere i dati da un file selezionato dall'utente, chiamare `OpenReadStream` sul file e leggere dal flusso restituito.</span><span class="sxs-lookup"><span data-stu-id="ad184-112">To read data from a user-selected file, call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="ad184-113">In un' Blazor WebAssembly applicazione, i dati vengono trasmessi direttamente nel codice .NET all'interno del browser.</span><span class="sxs-lookup"><span data-stu-id="ad184-113">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span> <span data-ttu-id="ad184-114">In un' Blazor Server app, i dati dei file vengono trasmessi nel codice .NET sul server mentre il file viene letto dal flusso.</span><span class="sxs-lookup"><span data-stu-id="ad184-114">In a Blazor Server app, the file data is streamed into .NET code on the server as the file is read from the stream.</span></span> 
