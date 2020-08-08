---
title: Risolvere i problemi di localizzazione di ASP.NET Core
author: hishamco
description: Informazioni su come diagnosticare i problemi di localizzazione nelle app ASP.NET Core.
ms.author: riande
ms.date: 01/24/2019
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
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 801ae3a708d23308cbf909ceeace160e3c0db1bd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016466"
---
# <a name="troubleshoot-aspnet-core-localization"></a>Risolvere i problemi di localizzazione di ASP.NET Core

Di [Hisham Bin Ateya](https://github.com/hishamco)

Questo articolo include istruzioni per la diagnostica di problemi di localizzazione delle app ASP.NET Core.

## <a name="localization-configuration-issues"></a>Problemi di configurazione della localizzazione

**Ordine del middleware di localizzazione**  
L'app potrebbe non essere localizzata perché il middleware di localizzazione non è ordinato come previsto.

Per risolvere questo problema assicurarsi che il middleware di localizzazione sia registrato prima del middleware MVC. In caso contrario il middleware di localizzazione non viene applicato.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**Percorso delle risorse di localizzazione non trovato**

**Le impostazioni cultura supportate in RequestCultureProvider non corrispondono con quelle registrate**  

## <a name="resource-file-naming-issues"></a>Problemi di denominazione dei file di risorse

ASP.NET Core include regole predefinite e linee guida per la denominazione dei file di risorse di localizzazione, descritte in dettaglio [qui](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).

## <a name="missing-resources"></a>Risorse mancanti

Le cause comuni per le quali non è possibile trovare le risorse sono:

- I nomi delle risorse presentano errori di ortografia nel file `resx` o nella richiesta del localizzatore.
- La risorsa non è presente in `resx` per alcune lingue, ma è presente per altre.
- Se si verificano ancora problemi, controllare i messaggi del log di localizzazione (al livello di log `Debug`) per altre informazioni sulle risorse mancanti.

_**Suggerimento:** Quando `CookieRequestCultureProvider` si utilizza, verificare che le virgolette singole non vengano utilizzate con le impostazioni cultura all'interno del valore di localizzazione cookie . Ad esempio, `c='en-UK'|uic='en-US'` è un valore non valido cookie , mentre `c=en-UK|uic=en-US` è un oggetto valido._

## <a name="resources--class-libraries-issues"></a>Problemi con le risorse e le librerie di classi

Per impostazione predefinita ASP.NET Core offre un modo per consentire alle librerie di classi di trovare i file di risorse tramite [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).

I problemi comuni con le librerie di classi includono:
- L'assenza di `ResourceLocationAttribute` in una libreria di classi impedisce a `ResourceManagerStringLocalizerFactory` di trovare le risorse.
- Denominazione dei file di risorse. Per altre informazioni, vedere la sezione [Problemi di denominazione dei file di risorse](#resource-file-naming-issues).
- Modifica dello spazio dei nomi radice della libreria di classi. Per altre informazioni, vedere la sezione [Problemi con lo spazio dei nomi radice](#root-namespace-issues).

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider non funziona come previsto

La classe `RequestLocalizationOptions` dispone di tre provider predefiniti:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) consente di personalizzare la modalità di fornitura delle impostazioni cultura di localizzazione nell'app. `CustomRequestCultureProvider` viene usato quando i provider predefiniti non soddisfano i requisiti.

- Un motivo comune per cui i il provider personalizzato non funziona correttamente è che non è il primo provider nell'elenco `RequestCultureProviders`. Per risolvere il problema:

- Inserire il provider personalizzato nella posizione 0 dell'elenco `RequestCultureProviders` come illustrato di seguito:

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- Usare il metodo di estensione `AddInitialRequestCultureProvider` per impostare il provider personalizzato come provider iniziale.

## <a name="root-namespace-issues"></a>Problemi con lo spazio dei nomi radice

Quando lo spazio dei nomi radice di un assembly è diverso dal nome dell'assembly, la localizzazione non funziona per impostazione predefinita. Per evitare questo problema usare [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), descritto in dettaglio [qui](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).

> [!WARNING]
> Questo problema può verificarsi quando il nome di un progetto non è un identificatore .NET valido. Ad esempio `my-project-name.csproj` , utilizzerà lo spazio dei nomi radice `my_project_name` e il nome dell'assembly `my-project-name` che genera questo errore. 

## <a name="resources--build-action"></a>Risorse e azione di compilazione

Se si usano file di risorse per la localizzazione, è importante che questi dispongano di un'azione di compilazione appropriata. Vanno impostati come **Risorsa incorporata**; in caso contrario `ResourceStringLocalizer` non è in grado di trovare tali risorse.
