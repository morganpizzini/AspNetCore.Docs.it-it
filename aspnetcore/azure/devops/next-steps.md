---
title: 'Passaggi successivi: DevOps con ASP.NET Core e Azure'
author: CamSoper
description: Percorsi di apprendimento aggiuntivi per DevOps con ASP.NET Core e Azure.Additional learning paths for DevOps with ASP.NET Core and Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: a775dc42551a43bcce72b5f9ca364ed00b1dc4e6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659474"
---
# <a name="next-steps"></a>Passaggi successivi

In questa guida è stata creata una pipeline DevOps per un'app di esempio ASP.NET Core.In this guide, you created a DevOps pipeline for an ASP.NET Core sample app. Congratulazioni! Ci auguriamo che ti sia piaciuto imparare a pubblicare ASP.NET app Web di base nel servizio app di Azure e automatizzare la continua integrazione delle modifiche.

Oltre all'hosting Web e a DevOps, Azure dispone di un'ampia gamma di servizi DiaS (Platform-as-a-Service) utili per ASP.NET gli sviluppatori Core. In questa sezione viene fornita una breve panoramica di alcuni dei servizi più comunemente utilizzati.

## <a name="storage-and-databases"></a>Archiviazione e database

[La cache Redis](/azure/redis-cache/) è la memorizzazione nella cache dei dati ad alta velocità effettiva e a bassa latenza disponibile come servizio. Può essere usato per memorizzare nella cache l'output della pagina, ridurre le richieste di database e fornire ASP.NET lo stato della sessione Core tra più istanze di un'app.

[Archiviazione di Azure](/azure/storage/) è l'archiviazione cloud massicciamente scalabile di Azure.Azure Storage is Azure's massively scalable cloud storage. Gli sviluppatori possono sfruttare [l'archiviazione delle code](/azure/storage/queues/storage-queues-introduction) per l'accodamento dei messaggi affidabile e [l'archiviazione tabelle](/azure/storage/tables/table-storage-overview) è un archivio chiave-valore NoSQL progettato per uno sviluppo rapido tramite set di dati semistrutturati di grandi dimensioni.

[Il database SQL](/azure/sql-database/) di Azure offre funzionalità di database relazionali familiari come servizio tramite Microsoft SQL Server Engine.Azure SQL Database provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.

[Cosmos DB](/azure/cosmos-db/) distribuito a livello globale, multimodello servizio di database NoSQL. Sono disponibili più API, tra cui L'API SQL (precedentemente denominata DocumentDB), Cassandra e MongoDB.

## <a name="identity"></a>Identità

[Azure Active Directory](/azure/active-directory/) e [Azure Active Directory B2C](/azure/active-directory-b2c/) sono entrambi servizi di identità. Azure Active Directory è progettato per scenari aziendali e abilita la collaborazione B2B (business-to-business) di Azure AD, mentre Azure Active Directory B2C è destinato agli scenari business-to-customer, incluso l'accesso ai social network.

## <a name="mobile"></a>Cellulare

[Notification Hubs](/azure/notification-hubs/) è un motore di notifica push scalabile multipiattaforma per inviare rapidamente milioni di messaggi alle app in esecuzione su vari tipi di dispositivi.

## <a name="web-infrastructure"></a>Infrastruttura Web

[Il servizio contenitore](/azure/aks/) di Azure gestisce l'ambiente Kubernetes ospitato, semplificando e velocizzando la distribuzione e la gestione di app containerizzate senza l'esperienza di orchestrazione dei contenitori.

[Ricerca di Azure](/azure/search/) viene usato per creare una soluzione di ricerca aziendale su contenuto privato e eterogeneo.

[Service Fabric](/azure/service-fabric/) è una piattaforma di sistemi distribuiti che semplifica il pacchetto, la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili.
