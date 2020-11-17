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
# <a name="aspnet-core-no-locblazor-file-uploads"></a>BlazorCaricamenti di file ASP.NET Core

Di [Daniel Roth](https://github.com/danroth27) [Pranav Krishnamoorthy](https://github.com/pranavkm)

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

Usare il `InputFile` componente per leggere i dati dei file del browser nel codice .NET, anche per i caricamenti di file.

> [!WARNING]
> Seguire sempre le procedure consigliate per la sicurezza di caricamento file. Per altre informazioni, vedere <xref:mvc/models/file-uploads#security-considerations>.

## <a name="inputfile-component"></a>Componente `InputFile`

Il componente viene sottoposta `InputFile` a rendering come input HTML di tipo `file` .

Per impostazione predefinita, l'utente seleziona singoli file. Aggiungere l' `multiple` attributo per consentire all'utente di caricare più file contemporaneamente. Quando uno o più file vengono selezionati dall'utente, il `InputFile` componente genera un `OnChange` evento e passa un oggetto `InputFileChangeEventArgs` che fornisce l'accesso all'elenco di file selezionati e informazioni dettagliate su ogni file.

Per leggere i dati da un file selezionato dall'utente:

* Chiamare `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` sul file e leggere dal flusso restituito. Per ulteriori informazioni, vedere la sezione [flussi di file](#file-streams) .
* L'oggetto <xref:System.IO.Stream> restituito da `OpenReadStream` impone una dimensione massima in byte dell'oggetto `Stream` letto. Per impostazione predefinita, le dimensioni dei file non superiori a 512.000 byte (500 KB) possono essere lette prima che altre letture provochino un'eccezione. Questo limite è presente per impedire agli sviluppatori di leggere accidentalmente file di grandi dimensioni in memoria. Il `maxAllowedSize` parametro in `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` può essere utilizzato per specificare una dimensione maggiore, se necessario.
* Evitare di leggere il flusso di file in ingresso direttamente in memoria. Ad esempio, non copiare i byte di file in un oggetto <xref:System.IO.MemoryStream> o leggere come matrice di byte. Questi approcci possono causare problemi di prestazioni e sicurezza, soprattutto in Blazor Server . In alternativa, è consigliabile copiare i byte dei file in un archivio esterno, ad esempio un BLOB o un file su disco.

Un componente che riceve un file di immagine può chiamare il `RequestImageFileAsync` metodo pratico sul file per ridimensionare i dati dell'immagine nel runtime JavaScript del browser prima che l'immagine venga trasmessa nell'app.

Nell'esempio seguente viene illustrato il caricamento di più file di immagine in un componente. `InputFileChangeEventArgs.GetMultipleFiles` consente la lettura di più file. Specificare il numero massimo di file che si prevede di leggere per impedire a un utente malintenzionato di caricare un numero maggiore di file rispetto a quanto previsto dall'app. `InputFileChangeEventArgs.File` consente di leggere il primo e l'unico file se il caricamento del file non supporta più file.

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

`IBrowserFile` restituisce [i metadati esposti dal browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) come proprietà. Questi metadati possono essere utili per la convalida preliminare. Vedere ad esempio i [ `FileUpload.razor` componenti di `FilePreview.razor` esempio e](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).

## <a name="file-streams"></a>Flussi di file

In un' Blazor WebAssembly applicazione, i dati vengono trasmessi direttamente nel codice .NET all'interno del browser.

In un' Blazor Server app, i dati dei file vengono trasmessi attraverso la SignalR connessione nel codice .NET sul server mentre il file viene letto dal flusso. [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) consente di configurare le caratteristiche di caricamento dei file per Blazor Server .

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
