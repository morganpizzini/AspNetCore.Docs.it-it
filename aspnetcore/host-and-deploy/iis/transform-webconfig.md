---
title: Trasformare web.config
author: rick-anderson
description: Informazioni su come trasformare il file web.config quando si pubblica un'app ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: 259b5bf9bf2a6de987494b5771897355e3ea67db
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057312"
---
# <a name="transform-webconfig"></a>Trasformare web.config

Di [Vijay](https://github.com/vijayrkn) ,

Le trasformazioni per il file *web.config* possono essere applicate automaticamente quando viene pubblicata un'app in base a:

* [Configurazione della build](#build-configuration)
* [Profilo](#profile)
* [Environment](#environment)
* [Impostazione personalizzata](#custom)

Queste trasformazioni si verificano per uno degli scenari di generazione di *web.config* seguenti:

* Generati automaticamente dall'SDK `Microsoft.NET.Sdk.Web`.
* Fornito dallo sviluppatore nella radice del [contenuto](xref:fundamentals/index#content-root) dell'app.

## <a name="build-configuration"></a>Configurare la compilazione

Le trasformazioni di configurazione della build vengono eseguite per prime.

Includere un file *web.{CONFIGURAZIONE}.config* per ogni [configurazione della build (Debug|Versione)](/dotnet/core/tools/dotnet-publish#options) che richiede una trasformazione di *web.config* .

Nell'esempio seguente, una variabile di ambiente specifica della configurazione viene impostata in *web.Release.config* :

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

La trasformazione viene applicata quando la configurazione è impostata su *Versione* :

```dotnetcli
dotnet publish --configuration Release
```

La proprietà di MSBuild per la configurazione è `$(Configuration)`.

## <a name="profile"></a>Profilo

Le trasformazioni di profilo vengono eseguite per seconde, dopo le trasformazioni di [configurazione della build](#build-configuration).

Includere un file *web.{PROFILO}.config* per ogni configurazione del profilo che richiede una trasformazione di *web.config* .

Nell'esempio seguente, una variabile di ambiente per il profilo è impostata in *web.FolderProfile.config* per un profilo di pubblicazione di cartella:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

La trasformazione viene applicata quando il profilo è *FolderProfile* :

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

La proprietà di MSBuild per il nome del profilo è `$(PublishProfile)`.

Se non viene passato alcun profilo, il nome del profilo predefinito è **FileSystem** e viene applicato *web.FileSystem.config* se il file è presente nella radice del contenuto dell'app.

## <a name="environment"></a>Ambiente

Le trasformazioni di ambiente vengono eseguite per terze, dopo le trasformazioni di [configurazione della build](#build-configuration) e di [profilo](#profile).

Includere un file *web.{AMBIENTE}.config* per ogni [ambiente](xref:fundamentals/environments) che richiede una trasformazione di *web.config* .

Nell'esempio seguente, una variabile di ambiente specifica dell'ambiente viene impostata in *web.Production.config* per l'ambiente Production:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

La trasformazione viene applicata quando l'ambiente è *Production* :

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

La proprietà di MSBuild per l'ambiente è `$(EnvironmentName)`.

Quando di esegue la pubblicazione da Visual Studio usando un profilo di pubblicazione, vedere <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.

La variabile di ambiente `ASPNETCORE_ENVIRONMENT` viene aggiunta automaticamente al file *Web.config* quando viene specificato il nome dell'ambiente.

## <a name="custom"></a>Personalizzato

Le trasformazioni personalizzate vengono eseguite per ultime, dopo le trasformazioni di [configurazione della build](#build-configuration), di [profilo](#profile) e di [ambiente](#environment).

Includere un file *{NOME_PERSONALIZZATO}.transform* per ogni configurazione personalizzata che richiede una trasformazione di *web.config* .

Nell'esempio seguente viene impostata una variabile di ambiente per una trasformazione personalizzata in *custom.transform* :

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

La trasformazione viene applicata al passaggio della proprietà `CustomTransformFileName` al comando [dotnet publish](/dotnet/core/tools/dotnet-publish):

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

La proprietà di MSBuild per il nome del profilo è `$(CustomTransformFileName)`.

## <a name="prevent-webconfig-transformation"></a>Impedire trasformazioni di web.config

Per impedire le trasformazioni del file *web.config* , impostare la proprietà MSBuild `$(IsWebConfigTransformDisabled)`:

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Risorse aggiuntive

* [Sintassi di trasformazione di Web.config per la distribuzione di un progetto di applicazione Web](/previous-versions/dd465326(v=vs.100))
* [Sintassi di trasformazione di Web.config per la distribuzione di un progetto Web tramite Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))
