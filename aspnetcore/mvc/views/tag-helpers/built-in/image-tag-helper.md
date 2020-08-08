---
title: Helper tag di immagine in ASP.NET Core
author: pkellner
description: Informazioni sull'uso dell'helper tag di immagine.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: a877078356b54a3d0bba6a1be24307e6e1c075c8
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018598"
---
# <a name="image-tag-helper-in-aspnet-core"></a>Helper tag di immagine in ASP.NET Core

Di [Peter Kellner](https://peterkellner.net)

L'helper tag di immagine migliora il tag `<img>` per rendere disponibile il comportamento di cache-busting per i file di immagine statici.

Una stringa di cache-busting è un valore univoco che rappresenta l'hash del file di immagine statico aggiunto all'URL dell'asset. La stringa univoca richiede ai client (e ad alcuni proxy) di ricaricare l'immagine dal server Web host e non dalla cache del client.

Se l'origine dell'immagine (`src`) è un file statico nel server Web host:

* Una stringa di cache-busting univoca viene aggiunta come parametro di query all'origine dell'immagine.
* Se il file nel server Web host cambia, viene generato un URL di richiesta univoco, che include il parametro di richiesta aggiornato.

Per una panoramica degli helper per tag, vedere <xref:mvc/views/tag-helpers/intro>.

## <a name="image-tag-helper-attributes"></a>Attributi dell'helper tag di immagine

### <a name="src"></a>src

Per attivare l'helper tag di immagine, è richiesto l'attributo `src` per l'elemento `<img>`.

L'origine dell'immagine (`src`) deve puntare a un file fisico statico nel server. Se `src` è un URI remoto, il parametro di stringa di query di cache-busting non viene generato.

### <a name="asp-append-version"></a>asp-append-version

Quando si specifica `asp-append-version` con un valore `true` insieme a un attributo `src`, viene chiamato l'helper tag di immagine.

L'esempio seguente usa un helper tag di immagine:

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

Se il file statico esiste nella directory */wwwroot/images/* il codice HTML generato è simile al seguente (l'hash sarà diverso):

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

Il valore assegnato al parametro `v` è il valore dell'hash del file *asplogo.png* su disco. Se il server Web non è in grado di ottenere l'accesso in lettura al file statico, non viene aggiunto alcun parametro `v` all'attributo `src` nel markup di cui viene eseguito il rendering.

## <a name="hash-caching-behavior"></a>Comportamento di memorizzazione nella cache dell'hash

L'helper tag di immagine usa il provider di cache nel server Web locale per archiviare l'hash `Sha512` calcolato di un determinato file. Se il file è richiesto più volte, l'hash non viene ricalcolato. La cache viene invalidata da un watcher di file che viene associato al file quando viene calcolato l'hash `Sha512` del file. Quando il file cambia su disco, viene calcolato e memorizzato nella cache un nuovo hash.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:performance/caching/memory>
