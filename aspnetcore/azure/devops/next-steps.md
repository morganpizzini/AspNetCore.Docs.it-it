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
# <a name="next-steps"></a><span data-ttu-id="46a79-103">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="46a79-103">Next steps</span></span>

<span data-ttu-id="46a79-104">In questa guida è stata creata una pipeline DevOps per un'app di esempio ASP.NET Core.In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span><span class="sxs-lookup"><span data-stu-id="46a79-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="46a79-105">Congratulazioni!</span><span class="sxs-lookup"><span data-stu-id="46a79-105">Congratulations!</span></span> <span data-ttu-id="46a79-106">Ci auguriamo che ti sia piaciuto imparare a pubblicare ASP.NET app Web di base nel servizio app di Azure e automatizzare la continua integrazione delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="46a79-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="46a79-107">Oltre all'hosting Web e a DevOps, Azure dispone di un'ampia gamma di servizi DiaS (Platform-as-a-Service) utili per ASP.NET gli sviluppatori Core.</span><span class="sxs-lookup"><span data-stu-id="46a79-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="46a79-108">In questa sezione viene fornita una breve panoramica di alcuni dei servizi più comunemente utilizzati.</span><span class="sxs-lookup"><span data-stu-id="46a79-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="46a79-109">Archiviazione e database</span><span class="sxs-lookup"><span data-stu-id="46a79-109">Storage and databases</span></span>

<span data-ttu-id="46a79-110">[La cache Redis](/azure/redis-cache/) è la memorizzazione nella cache dei dati ad alta velocità effettiva e a bassa latenza disponibile come servizio.</span><span class="sxs-lookup"><span data-stu-id="46a79-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="46a79-111">Può essere usato per memorizzare nella cache l'output della pagina, ridurre le richieste di database e fornire ASP.NET lo stato della sessione Core tra più istanze di un'app.</span><span class="sxs-lookup"><span data-stu-id="46a79-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="46a79-112">[Archiviazione di Azure](/azure/storage/) è l'archiviazione cloud massicciamente scalabile di Azure.Azure Storage is Azure's massively scalable cloud storage.</span><span class="sxs-lookup"><span data-stu-id="46a79-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="46a79-113">Gli sviluppatori possono sfruttare [l'archiviazione delle code](/azure/storage/queues/storage-queues-introduction) per l'accodamento dei messaggi affidabile e [l'archiviazione tabelle](/azure/storage/tables/table-storage-overview) è un archivio chiave-valore NoSQL progettato per uno sviluppo rapido tramite set di dati semistrutturati di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="46a79-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="46a79-114">[Il database SQL](/azure/sql-database/) di Azure offre funzionalità di database relazionali familiari come servizio tramite Microsoft SQL Server Engine.Azure SQL Database provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span><span class="sxs-lookup"><span data-stu-id="46a79-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="46a79-115">[Cosmos DB](/azure/cosmos-db/) distribuito a livello globale, multimodello servizio di database NoSQL.</span><span class="sxs-lookup"><span data-stu-id="46a79-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="46a79-116">Sono disponibili più API, tra cui L'API SQL (precedentemente denominata DocumentDB), Cassandra e MongoDB.</span><span class="sxs-lookup"><span data-stu-id="46a79-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## <a name="identity"></a><span data-ttu-id="46a79-117">Identità</span><span class="sxs-lookup"><span data-stu-id="46a79-117">Identity</span></span>

<span data-ttu-id="46a79-118">[Azure Active Directory](/azure/active-directory/) e [Azure Active Directory B2C](/azure/active-directory-b2c/) sono entrambi servizi di identità.</span><span class="sxs-lookup"><span data-stu-id="46a79-118">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="46a79-119">Azure Active Directory è progettato per scenari aziendali e abilita la collaborazione B2B (business-to-business) di Azure AD, mentre Azure Active Directory B2C è destinato agli scenari business-to-customer, incluso l'accesso ai social network.</span><span class="sxs-lookup"><span data-stu-id="46a79-119">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="46a79-120">Cellulare</span><span class="sxs-lookup"><span data-stu-id="46a79-120">Mobile</span></span>

<span data-ttu-id="46a79-121">[Notification Hubs](/azure/notification-hubs/) è un motore di notifica push scalabile multipiattaforma per inviare rapidamente milioni di messaggi alle app in esecuzione su vari tipi di dispositivi.</span><span class="sxs-lookup"><span data-stu-id="46a79-121">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="46a79-122">Infrastruttura Web</span><span class="sxs-lookup"><span data-stu-id="46a79-122">Web infrastructure</span></span>

<span data-ttu-id="46a79-123">[Il servizio contenitore](/azure/aks/) di Azure gestisce l'ambiente Kubernetes ospitato, semplificando e velocizzando la distribuzione e la gestione di app containerizzate senza l'esperienza di orchestrazione dei contenitori.</span><span class="sxs-lookup"><span data-stu-id="46a79-123">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="46a79-124">[Ricerca di Azure](/azure/search/) viene usato per creare una soluzione di ricerca aziendale su contenuto privato e eterogeneo.</span><span class="sxs-lookup"><span data-stu-id="46a79-124">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="46a79-125">[Service Fabric](/azure/service-fabric/) è una piattaforma di sistemi distribuiti che semplifica il pacchetto, la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili.</span><span class="sxs-lookup"><span data-stu-id="46a79-125">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
