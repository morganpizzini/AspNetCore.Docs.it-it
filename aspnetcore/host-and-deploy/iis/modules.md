---
title: Moduli IIS con ASP.NET Core
author: rick-anderson
description: Individuare i moduli IIS attivi e inattivi per le app ASP.NET Core e come gestire i moduli IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 7262b9ea18e4cf6acd278d087fcc44262f8f9c80
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775947"
---
# <a name="iis-modules-with-aspnet-core"></a>Moduli IIS con ASP.NET Core

Alcuni dei moduli nativi e tutti i moduli gestiti di IIS non sono disponibili per l'elaborazione delle richieste per le app ASP.NET Core. In molti casi, ASP.NET Core offre un'alternativa agli scenari gestiti dai moduli nativi e gestiti di IIS.

## <a name="native-modules"></a>Moduli nativi

La tabella indica i moduli di IIS nativi che funzionano con le app ASP.NET Core e il modulo ASP.NET Core.

| Modulo | Funzionante con le app ASP.NET Core | Opzione di ASP.NET Core |
| --- | :---: | --- |
| **Autenticazione anonima**<br>`AnonymousAuthenticationModule`                                  | Sì | |
| **Autenticazione di base**<br>`BasicAuthenticationModule`                                          | Sì | |
| **Autenticazione mapping certificazione client**<br>`CertificateMappingAuthenticationModule`      | Sì | |
| **CGI**<br>`CgiModule`                                                                           | No  | |
| **Convalida della configurazione**<br>`ConfigurationValidationModule`                                  | Sì | |
| **Errori HTTP**<br>`CustomErrorModule`                                                           | No  | [Middleware delle tabelle codici di stato](xref:fundamentals/error-handling#usestatuscodepages) |
| **Registrazione personalizzata**<br>`CustomLoggingModule`                                                      | Sì | |
| **Documento predefinito**<br>`DefaultDocumentModule`                                                  | No  | [Middleware dei file predefiniti](xref:fundamentals/static-files#serve-a-default-document) |
| **Autenticazione del digest**<br>`DigestAuthenticationModule`                                        | Sì | |
| **Esplorazione directory**<br>`DirectoryListingModule`                                               | No  | [Middleware di esplorazione directory](xref:fundamentals/static-files#enable-directory-browsing) |
| **Compressione dinamica**<br>`DynamicCompressionModule`                                            | Sì | [Middleware di compressione delle risposte](xref:performance/response-compression) |
| **Traccia delle richieste non riuscite**<br>`FailedRequestsTracingModule`                                     | Sì | [Registrazione ASP.NET Core](xref:fundamentals/logging/index#tracesource-provider) |
| **Memorizzazione nella cache dei file**<br>`FileCacheModule`                                                            | No  | [Middleware di memorizzazione nella cache delle risposte](xref:performance/caching/middleware) |
| **Memorizzazione nella cache HTTP**<br>`HttpCacheModule`                                                            | No  | [Middleware di memorizzazione nella cache delle risposte](xref:performance/caching/middleware) |
| **Registrazione HTTP**<br>`HttpLoggingModule`                                                          | Sì | [Registrazione ASP.NET Core](xref:fundamentals/logging/index) |
| **Reindirizzamento HTTP**<br>`HttpRedirectionModule`                                                  | Sì | [Middleware di riscrittura URL](xref:fundamentals/url-rewriting) |
| **Traccia HTTP**<br>`TracingModule`                                                              | Sì | |
| **Autenticazione mapping certificati client IIS**<br>`IISCertificateMappingAuthenticationModule` | Sì | |
| **Restrizioni per IP e domini**<br>`IpRestrictionModule`                                          | Sì | |
| **Filtri ISAPI**<br>`IsapiFilterModule`                                                         | Sì | [Middleware](xref:fundamentals/middleware/index) |
| **ISAPI**<br>`IsapiModule`                                                                       | Sì | [Middleware](xref:fundamentals/middleware/index) |
| **Protocolli supportati**<br>`ProtocolSupportModule`                                                  | Sì | |
| **Filtro richieste**<br>`RequestFilteringModule`                                                | Sì | [Middleware di riscrittura URL`IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **Monitoraggio richieste**<br>`RequestMonitorModule`                                                    | Sì | |
| **Riscrittura degli URL**&#8224;<br>`RewriteModule`                                                      | Sì | [Middleware di riscrittura URL](xref:fundamentals/url-rewriting) |
| **Inclusioni lato server**<br>`ServerSideIncludeModule`                                            | No  | |
| **Compressione statica**<br>`StaticCompressionModule`                                              | No  | [Middleware di compressione delle risposte](xref:performance/response-compression) |
| **Contenuto statico**<br>`StaticFileModule`                                                         | No  | [Middleware dei file statici](xref:fundamentals/static-files) |
| **Memorizzazione di token nella cache**<br>`TokenCacheModule`                                                          | Sì | |
| **Memorizzazione nella cache degli URI**<br>`UriCacheModule`                                                              | Sì | |
| **Autorizzazione URL**<br>`UrlAuthorizationModule`                                                | Sì | [Identità di ASP.NET Core](xref:security/authentication/identity) |
| **Autenticazione di Windows**<br>`WindowsAuthenticationModule`                                      | Sì | |

&#8224;I tipi corrispondenti `isFile` e `isDirectory` di URL Rewrite Module non funzionano con le app ASP.NET Core a causa delle modifiche apportate alla [struttura di directory](xref:host-and-deploy/directory-structure).

## <a name="managed-modules"></a>Moduli gestiti

I moduli gestiti *non* funzionano con le app ASP.NET Core ospitate quando la versione CLR .NET del pool di app è impostata su **Nessun codice gestito**. In molti casi, ASP.NET Core offre alternative a livello di middleware.

| Modulo                  | Opzione di ASP.NET Core |
| ----------------------- | ------------------- |
| AnonymousIdentification | |
| DefaultAuthentication   | |
| FileAuthorization       | |
| FormsAuthentication     | [Cookie del middleware di autenticazione](xref:security/authentication/cookie) |
| OutputCache             | [Middleware di memorizzazione nella cache delle risposte](xref:performance/caching/middleware) |
| Profilo                 | |
| RoleManager             | |
| ScriptModule-4.0        | |
| sessione                 | [Middleware di sessione](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModule       | [Middleware di riscrittura URL](xref:fundamentals/url-rewriting) |
| UrlRoutingModule-4.0    | [ASP.NET CoreIdentity](xref:security/authentication/identity) |
| WindowsAuthentication   | |

## <a name="iis-manager-application-changes"></a>Modifiche dell'applicazione Gestione IIS

Quando si usa Gestione IIS per configurare le impostazioni, il file *web.config* dell'app viene modificato. Se si distribuisce un'app e si include *web.config*, le eventuali modifiche apportate con Gestione IIS vengono sovrascritte dal file *web.config* distribuito. Se vengono apportate modifiche al file *web.config* sul server, copiare immediatamente il file *web.config* aggiornato sul server nel progetto locale.

## <a name="disabling-iis-modules"></a>Disabilitazione dei moduli IIS

Se un modulo IIS configurato a livello di server deve essere disabilitato per un'app, un'aggiunta al file *web.config* dell'app consente di disabilitare il modulo. È possibile mantenere il modulo e disattivarlo tramite un'impostazione di configurazione (se disponibile) oppure rimuovere il modulo dall'app.

### <a name="module-deactivation"></a>Disattivazione del modulo

Molti moduli offrono un'impostazione di configurazione che consente di disabilitarli senza rimuovere il modulo dall'app. Questo è il modo più semplice e rapido per disattivare un modulo. Ad esempio, il modulo di reindirizzamento HTTP può essere disabilitato tramite l'elemento `<httpRedirect>` in *web.config*:

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Per altre informazioni sulla disabilitazione dei moduli con le impostazioni di configurazione, usare i collegamenti nella sezione *Elementi figlio* di [IIS \<system.webServer>](/iis/configuration/system.webServer/).

### <a name="module-removal"></a>Rimozione dei moduli

Se si sceglie di rimuovere un modulo con un'impostazione in *web.config*, sbloccare innanzitutto il modulo e la sezione `<modules>` di *web.config*:

1. Sbloccare il modulo a livello di server. Selezionare il server IIS nella barra laterale **Connessioni** di Gestione IIS. Aprire **Moduli** nell'area **IIS**. Selezionare il modulo nell'elenco. Nella barra laterale **Azioni** sulla destra selezionare **Sblocca**. Se la voce di azione per il modulo viene visualizzata come **Blocca**, il modulo è già sbloccato e non è richiesta alcuna azione. Sbloccare tutti i moduli che si prevede di rimuovere da *web.config* in un secondo momento.

2. Distribuire l'app senza una `<modules>` sezione nel *file Web. config*. Se un'app viene distribuita con un *file Web. config* contenente la `<modules>` sezione senza che sia stata sbloccata la sezione per la prima volta in gestione IIS, il Configuration Manager genera un'eccezione durante il tentativo di sbloccare la sezione. Di conseguenza, distribuire l'app senza una sezione `<modules>`.

3. Sbloccare la `<modules>` sezione del *file Web. config*. Nella barra laterale **connessioni** selezionare il sito Web in **siti**. Nell'area **Gestione** aprire **Editor configurazione**. Usare i controlli di navigazione per selezionare la sezione `system.webServer/modules`. Nella barra laterale **Azioni** sulla destra selezionare **Sblocca** per la sezione. Se la voce di azione per la sezione del modulo viene visualizzata come **Blocca sezione**, la sezione del modulo è già sbloccata e non è richiesta alcuna azione.

4. Aggiungere una sezione `<modules>` al file *web.config* locale dell'app con un elemento `<remove>` per rimuovere il modulo dall'app. Aggiungere più elementi `<remove>` per rimuovere più moduli. Se le modifiche a *web.config* vengono apportate sul server, apportare immediatamente le stesse modifiche al file *web.config* del progetto in locale. La rimozione di un modulo in questo modo non influisce sull'uso del modulo con le altre app nel server.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

Per aggiungere o rimuovere i moduli per IIS Express con *web.config*, modificare *applicationHost.config* per sbloccare la sezione `<modules>`:

1. Aprire *{APPLICATION ROOT}\\.vs\config\applicationhost.config*.

1. Individuare l'elemento `<section>` per i moduli IIS e modificare `overrideModeDefault` da `Deny` a `Allow`:

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. Individuare la sezione `<location path="" overrideMode="Allow"><system.webServer><modules>`. Per tutti i moduli che si desidera rimuovere, impostare `lockItem` da `true` a `false`. Nell'esempio seguente il modulo CGI è sbloccato:

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. Dopo aver sbloccato la sezione `<modules>` e i singoli moduli, è possibile aggiungere o rimuovere i moduli IIS con il file *web.config* dell'app per l'esecuzione dell'app in IIS Express.

Un modulo IIS può anche essere rimosso con *Appcmd.exe*. Specificare `MODULE_NAME` e `APPLICATION_NAME` nel comando:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Ad esempio, rimuovere `DynamicCompressionModule` dal sito Web predefinito:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>Configurazione minima dei moduli

I soli moduli necessari per eseguire un'app ASP.NET Core sono il modulo di autenticazione anonima e il modulo ASP.NET Core.

Il modulo di memorizzazione nella cache degli URI (`UriCacheModule`) consente a IIS di memorizzare nella cache la configurazione del sito Web a livello di URL. Senza questo modulo, IIS deve leggere e analizzare la configurazione a ogni richiesta, anche quando lo stesso URL viene richiesto ripetutamente. L'analisi della configurazione a ogni richiesta comporta una riduzione significativa delle prestazioni. *Anche se il modulo di memorizzazione nella cache degli URI non è strettamente necessario per l'esecuzione di un'app ASP.NET Core ospitata, è consigliabile abilitare il modulo di memorizzazione nella cache degli URI per tutte le distribuzioni di ASP.NET Core.*

Il modulo di memorizzazione nella cache HTTP (`HttpCacheModule`) implementa la cache di output di IIS e anche la logica per la memorizzazione degli elementi nella cache HTTP.sys. Senza questo modulo, il contenuto non viene più memorizzato nella cache in modalità kernel e i profili cache vengono ignorati. La rimozione del modulo di memorizzazione nella cache HTTP in genere ha effetti negativi sulle prestazioni e l'uso delle risorse. *Anche se il modulo di memorizzazione nella cache HTTP non è strettamente necessario per l'esecuzione di un'app ASP.NET Core ospitata, è consigliabile abilitare il modulo di memorizzazione nella cache HTTP per tutte le distribuzioni di ASP.NET Core.*

## <a name="additional-resources"></a>Risorse aggiuntive

* [Introduction to IIS Architectures: Modules in IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis) (Introduzione alle architetture IIS: moduli di IIS)
* [IIS Modules Overview](/iis/get-started/introduction-to-iis/iis-modules-overview) (Panoramica dei moduli IIS)
* [Customizing IIS 7.0 Roles and Modules](https://technet.microsoft.com/library/cc627313.aspx) (Personalizzazione di ruoli e moduli di IIS 7.0)
* [>\<IIS System. webserver](/iis/configuration/system.webServer/)
