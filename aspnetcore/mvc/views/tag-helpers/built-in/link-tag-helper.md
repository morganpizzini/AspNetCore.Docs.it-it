---
title: Helper tag di collegamento in ASP.NET Core
author: rick-anderson
ms.author: riande
description: Individuare gli attributi dell'helper tag di collegamento ASP.NET Core e il ruolo di ciascun attributo per estendere il comportamento del tag di collegamento HTML.
ms.custom: mvc
ms.date: 09/24/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: c767ff4c2a1e0d5d10ccb3ff855126f541c04f64
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408240"
---
# <a name="link-tag-helper-in-aspnet-core"></a>Helper tag di collegamento in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

L' [Helper tag di collegamento](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) genera un collegamento a un file CSS primario o di fallback. In genere il file CSS primario si trova in una rete per la [distribuzione di contenuti](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

L'helper tag di collegamento consente di specificare una rete CDN per il file CSS e un fallback quando la rete CDN non è disponibile. L'helper tag di collegamento offre il vantaggio delle prestazioni di una rete CDN con l'affidabilità dell'hosting locale.

Il Razor markup seguente mostra l' `head` elemento di un file di layout creato con il modello di app web di ASP.NET Core:

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

Di seguito viene eseguito il rendering del codice HTML dal codice precedente (in un ambiente non di sviluppo):

[!code-csharp[](link-tag-helper/sample/HtmlPage1.html)]

Nel codice precedente, l'helper tag di collegamento ha generato l' `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` elemento e il seguente codice JavaScript usato per verificare che il file *bootstrap. min. CSS* richiesto sia disponibile nella rete CDN. In questo caso, il file CSS era disponibile, in modo che l'helper Tag generasse l' `<link />` elemento con il file CSS della rete CDN.

## <a name="commonly-used-link-tag-helper-attributes"></a>Attributi di helper tag di collegamento comunemente utilizzati

Vedere [Helper tag di collegamento](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) per tutti gli attributi, le proprietà e i metodi dell'helper tag di collegamento.

### <a name="href"></a>href

Indirizzo preferenziale della risorsa collegata. All'indirizzo viene passato un pensiero al codice HTML generato in tutti i casi.

### <a name="asp-fallback-href"></a>ASP-fallback-href

URL di un foglio di stile CSS di cui eseguire il fallback in caso di errore dell'URL primario.

### <a name="asp-fallback-test-class"></a>ASP-fallback-test-Class

Nome della classe definito nel foglio di stile da utilizzare per il test di fallback. Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.

### <a name="asp-fallback-test-property"></a>ASP-fallback-test-proprietà

Nome della proprietà CSS da utilizzare per il test di fallback. Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.

### <a name="asp-fallback-test-value"></a>ASP-fallback-test-value

Valore della proprietà CSS da utilizzare per il test di fallback. Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
