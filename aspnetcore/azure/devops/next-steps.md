---
title: Passaggi successivi-DevOps con ASP.NET Core e Azure
author: CamSoper
description: Percorsi di apprendimento aggiuntivi per DevOps con ASP.NET Core e Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/next-steps
ms.openlocfilehash: 92401d45d36dd3b93d175e08a8fa8697217ca7c7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766524"
---
# <a name="next-steps"></a>Passaggi successivi

In questa guida è stata creata una pipeline DevOps per un'app di esempio ASP.NET Core. Congratulazioni! Ci auguriamo che tu abbia goduto di imparare a pubblicare ASP.NET Core app Web per app Azure servizio e automatizzare l'integrazione continuativa delle modifiche.

Oltre all'hosting Web e a DevOps, Azure offre un'ampia gamma di servizi PaaS (Platform-as-a-Service) utili per ASP.NET Core gli sviluppatori. Questa sezione fornisce una breve panoramica di alcuni dei servizi usati più di frequente.

## <a name="storage-and-databases"></a>Archiviazione e database

[Cache Redis](/azure/redis-cache/) è un'elevata velocità effettiva e la memorizzazione nella cache dei dati a bassa latenza disponibile come servizio. Può essere usato per memorizzare nella cache l'output della pagina, ridurre le richieste di database e fornire ASP.NET Core stato della sessione tra più istanze di un'app.

[Archiviazione di Azure](/azure/storage/) è una risorsa di archiviazione cloud estremamente scalabile di Azure. Gli sviluppatori possono sfruttare i vantaggi dell' [archiviazione code](/azure/storage/queues/storage-queues-introduction) per l'accodamento dei messaggi affidabile e l' [archiviazione tabelle](/azure/storage/tables/table-storage-overview) è un archivio chiave-valore NoSQL progettato per lo sviluppo rapido usando set di dati di grandi dimensioni e semi-strutturati.

Il [database SQL di Azure](/azure/sql-database/) offre funzionalità di database relazionali familiari come un servizio che usa il motore di Microsoft SQL Server.

[Cosmos DB](/azure/cosmos-db/) servizio di database NoSQL multimodello distribuito a livello globale. Sono disponibili più API, tra cui l'API SQL (denominata in precedenza DocumentDB), Cassandra e MongoDB.

## Identity

[Azure Active Directory](/azure/active-directory/) e [Azure Active Directory B2C](/azure/active-directory-b2c/) sono entrambi servizi di identità. Azure Active Directory è progettato per gli scenari aziendali e consente la collaborazione Azure AD B2B (business to business), mentre Azure Active Directory B2C è concepita per gli scenari business-to-Customer, incluso l'accesso ai social network.

## <a name="mobile"></a>Cellulare

[Hub di notifica](/azure/notification-hubs/) è un motore di notifiche push multipiattaforma e scalabile che consente di inviare rapidamente milioni di messaggi alle app in esecuzione su diversi tipi di dispositivi.

## <a name="web-infrastructure"></a>Infrastruttura Web

Il [servizio contenitore di Azure](/azure/aks/) gestisce l'ambiente Kubernetes ospitato, semplificando e semplificando la distribuzione e la gestione delle app incluse in contenitori senza competenze nell'orchestrazione di contenitori.

[Ricerca di Azure](/azure/search/) viene usato per creare una soluzione di ricerca aziendale sul contenuto privato eterogeneo.

[Service Fabric](/azure/service-fabric/) è una piattaforma di sistemi distribuiti che semplifica la disposizione di pacchetti, la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili.
