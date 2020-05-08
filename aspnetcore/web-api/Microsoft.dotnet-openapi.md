---
title: Sviluppare app ASP.NET Core usando OpenAPI
author: ryanbrandenburg
description: Viene illustrato come utilizzare lo strumento ' Microsoft. dotnet-openapi ' per aggiungere riferimenti a file OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 1924fb8ee5ac1ba8dc31d2175a336c8333c81fb2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775713"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>Sviluppare app ASP.NET Core usando gli strumenti di OpenAPI

Di Ryan Brandenburg

[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) è uno [strumento globale .NET Core](/dotnet/core/tools/global-tools) per la gestione dei riferimenti [openapi](https://github.com/OAI/OpenAPI-Specification) all'interno di un progetto.

## <a name="installation"></a>Installazione

Per installare `Microsoft.dotnet-openapi`, eseguire il comando seguente:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a>Add

L'aggiunta di un riferimento OpenAPI usando uno qualsiasi dei comandi in questa pagina `<OpenApiReference />` aggiunge un elemento simile al seguente al file con *estensione csproj* :

```xml
<OpenApiReference Include="openapi.json" />
```

Il riferimento precedente è necessario per l'app per chiamare il codice client generato.

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a>Aggiungi file

#### <a name="options"></a>Opzioni

| Short-opzione| Opzione Long| Descrizione | Esempio |
|-------|------|-------|---------|
| -p|--updateProject | Progetto su cui operare. |file di aggiunta di DotNet openapi *--updateProject .\Ref.csproj* .\OpenAPI.JSON |
| -c|--generatore di codice| Generatore di codice da applicare al riferimento. Le opzioni `NSwagCSharp` sono `NSwagTypeScript`e. Se `--code-generator` non viene specificato `NSwagCSharp`, l'impostazione predefinita degli strumenti è.|DotNet openapi Add file .\OpenApi.json--Code-Generator
| -H|--help|Mostra le informazioni della Guida|file di aggiunta di DotNet openapi--Help|

#### <a name="arguments"></a>Argomenti

|  Argomento  | Descrizione | Esempio |
|-------------|-------------|---------|
| file di origine | Origine da cui creare un riferimento. Deve essere un file OpenAPI. |file DotNet openapi Add *.\OpenAPI.JSON* |

### <a name="add-url"></a>Aggiungere URL

#### <a name="options"></a>Opzioni

| Short-opzione| Opzione Long| Descrizione | Esempio |
|-------|------|-------------|---------|
| -p|--updateProject | Progetto su cui operare. |URL di aggiunta di DotNet openapi *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json` |
| -o|--output-file | Posizione in cui inserire la copia locale del file OpenAPI. |DotNet openapi Add URL `https://contoso.com/openapi.json` *--output-file client. JSON* |
| -c|--generatore di codice| Generatore di codice da applicare al riferimento. Le opzioni `NSwagCSharp` sono `NSwagTypeScript`e. |DotNet openapi Add file .\OpenApi.json--Code-Generator
| -H|--help|Mostra le informazioni della Guida|URL di aggiunta di DotNet openapi--Help|

#### <a name="arguments"></a>Argomenti

|  Argomento  | Descrizione | Esempio |
|-------------|-------------|---------|
| URL origine | Origine da cui creare un riferimento. Deve essere un URL. |URL di aggiunta di DotNet openapi`https://contoso.com/openapi.json` |

## <a name="remove"></a>Rimuovere

Rimuove il riferimento OpenAPI che corrisponde al nome file specificato dal file con *estensione csproj* . Quando viene rimosso il riferimento OpenAPI, i client non verranno generati. I file local *. JSON* e *. YAML* vengono eliminati.

### <a name="options"></a>Opzioni

| Short-opzione| Opzione Long| Descrizione| Esempio |
|-------|------|------------|---------|
| -p|--updateProject | Progetto su cui operare. |DotNet openapi Remove *--updateProject .\Ref.csproj* .\OpenAPI.JSON |
| -H|--help|Mostra le informazioni della Guida|openapi di DotNet--Guida|

### <a name="arguments"></a>Argomenti

|  Argomento  | Descrizione| Esempio |
| ------------|------------|---------|
| file di origine | Origine a cui rimuovere il riferimento. |*.\OpenAPI.JSON* DotNet openapi Remove |

## <a name="refresh"></a>Aggiorna

Aggiorna la versione locale di un file scaricato usando il contenuto più recente dell'URL di download.

### <a name="options"></a>Opzioni

| Short-opzione| Opzione Long| Descrizione | Esempio |
|-------|------|-------------|---------|
| -p|--updateProject | Progetto su cui operare. | aggiornamento DotNet openapi *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json` |
| -H|--help|Mostra le informazioni della Guida|aggiornamento di DotNet openapi--Help|

### <a name="arguments"></a>Argomenti

|  Argomento  | Descrizione | Esempio |
| ------------|-------------|---------|
| URL origine | URL da cui aggiornare il riferimento. | aggiornamento DotNet openapi`https://contoso.com/openapi.json` |
