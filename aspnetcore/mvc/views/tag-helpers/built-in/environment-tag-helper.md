---
title: Helper tag di ambiente in ASP.NET Core
author: pkellner
description: Helper tag di ambiente ASP.NET Core definito con tutte le proprietà
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 144cc8988ba5797265b38f0f7364f528e0dbb97e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777423"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Helper tag di ambiente in ASP.NET Core

Di [Peter Kellner](https://peterkellner.net) e [Hisham Bin Ateya](https://twitter.com/hishambinateya)

L'helper tag di ambiente esegue in modo condizionale il rendering del contenuto racchiuso in base all' [ambiente host](xref:fundamentals/environments)corrente. L'unico attributo dell'helper tag di ambiente, `names`, è un elenco delimitato da virgole di nomi di ambiente. Se nessuno dei nomi di ambiente specificato corrisponde all'ambiente corrente, viene eseguito il rendering del contenuto incluso.

Per una panoramica degli helper per tag, vedere <xref:mvc/views/tag-helpers/intro>.

## <a name="environment-tag-helper-attributes"></a>Attributi dell'helper tag di ambiente

### <a name="names"></a>nomi

`names` accetta un singolo nome di ambiente host o un elenco delimitato da virgole di nomi di ambiente, che attiva il rendering del contenuto.

I valori di ambiente vengono confrontati con il valore corrente restituito da [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*). Il confronto non applica la distinzione tra maiuscole e minuscole.

L'esempio seguente usa un helper tag di ambiente. Il rendering del contenuto viene eseguito se l'ambiente host è Staging o Production:

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>Attributi include ed exclude

`include`& gli attributi controllano il rendering del contenuto racchiuso in base ai nomi degli ambienti host inclusi o esclusi `exclude` .

### <a name="include"></a>include

La proprietà `include` ha un comportamento simile all'attributo `names`. Un ambiente elencato nel valore dell'attributo `include` deve corrispondere all'ambiente host dell'app ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) per il rendering del contenuto del tag `<environment>`.

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>exclude

Al contrario dell'attributo `include`, il rendering del contenuto del tag `<environment>` viene eseguito quando l'ambiente host non corrisponde a un ambiente elencato nel valore dell'attributo `exclude`.

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/environments>
