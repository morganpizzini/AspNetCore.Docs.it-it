---
title: Passaggi successivi-DevOps con ASP.NET Core e Azure
author: CamSoper
description: Percorsi di apprendimento aggiuntivi per DevOps con ASP.NET Core e Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/next-steps
ms.openlocfilehash: 35b0486611cc15f25b1c8b54584c264e4c1298c9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056595"
---
# <a name="next-steps"></a><span data-ttu-id="632a9-103">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="632a9-103">Next steps</span></span>

<span data-ttu-id="632a9-104">In questa guida è stata creata una pipeline DevOps per un'app di esempio ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="632a9-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="632a9-105">La procedura è stata completata.</span><span class="sxs-lookup"><span data-stu-id="632a9-105">Congratulations!</span></span> <span data-ttu-id="632a9-106">Ci auguriamo che tu abbia goduto di imparare a pubblicare ASP.NET Core app Web per app Azure servizio e automatizzare l'integrazione continuativa delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="632a9-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="632a9-107">Oltre all'hosting Web e a DevOps, Azure offre un'ampia gamma di servizi PaaS (Platform-as-a-Service) utili per ASP.NET Core gli sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="632a9-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="632a9-108">Questa sezione fornisce una breve panoramica di alcuni dei servizi usati più di frequente.</span><span class="sxs-lookup"><span data-stu-id="632a9-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="632a9-109">Archiviazione e database</span><span class="sxs-lookup"><span data-stu-id="632a9-109">Storage and databases</span></span>

<span data-ttu-id="632a9-110">[Cache Redis](/azure/redis-cache/) è un'elevata velocità effettiva e la memorizzazione nella cache dei dati a bassa latenza disponibile come servizio.</span><span class="sxs-lookup"><span data-stu-id="632a9-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="632a9-111">Può essere usato per memorizzare nella cache l'output della pagina, ridurre le richieste di database e fornire ASP.NET Core stato della sessione tra più istanze di un'app.</span><span class="sxs-lookup"><span data-stu-id="632a9-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="632a9-112">[Archiviazione di Azure](/azure/storage/) è una risorsa di archiviazione cloud estremamente scalabile di Azure.</span><span class="sxs-lookup"><span data-stu-id="632a9-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="632a9-113">Gli sviluppatori possono sfruttare i vantaggi dell' [archiviazione code](/azure/storage/queues/storage-queues-introduction) per l'accodamento dei messaggi affidabile e l' [archiviazione tabelle](/azure/storage/tables/table-storage-overview) è un archivio chiave-valore NoSQL progettato per lo sviluppo rapido usando set di dati di grandi dimensioni e semi-strutturati.</span><span class="sxs-lookup"><span data-stu-id="632a9-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="632a9-114">Il [database SQL di Azure](/azure/sql-database/) offre funzionalità di database relazionali familiari come un servizio che usa il motore di Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="632a9-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="632a9-115">[Cosmos DB](/azure/cosmos-db/) servizio di database NoSQL multimodello distribuito a livello globale.</span><span class="sxs-lookup"><span data-stu-id="632a9-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="632a9-116">Sono disponibili più API, tra cui l'API SQL (denominata in precedenza DocumentDB), Cassandra e MongoDB.</span><span class="sxs-lookup"><span data-stu-id="632a9-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## Identity

<span data-ttu-id="632a9-117">[Azure Active Directory](/azure/active-directory/) e [Azure Active Directory B2C](/azure/active-directory-b2c/) sono entrambi servizi di identità.</span><span class="sxs-lookup"><span data-stu-id="632a9-117">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="632a9-118">Azure Active Directory è progettato per gli scenari aziendali e consente la collaborazione Azure AD B2B (business to business), mentre Azure Active Directory B2C è concepita per gli scenari business-to-Customer, incluso l'accesso ai social network.</span><span class="sxs-lookup"><span data-stu-id="632a9-118">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="632a9-119">Mobile</span><span class="sxs-lookup"><span data-stu-id="632a9-119">Mobile</span></span>

<span data-ttu-id="632a9-120">[Hub di notifica](/azure/notification-hubs/) è un motore di notifiche push multipiattaforma e scalabile che consente di inviare rapidamente milioni di messaggi alle app in esecuzione su diversi tipi di dispositivi.</span><span class="sxs-lookup"><span data-stu-id="632a9-120">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="632a9-121">Infrastruttura Web</span><span class="sxs-lookup"><span data-stu-id="632a9-121">Web infrastructure</span></span>

<span data-ttu-id="632a9-122">Il [servizio contenitore di Azure](/azure/aks/) gestisce l'ambiente Kubernetes ospitato, semplificando e semplificando la distribuzione e la gestione delle app incluse in contenitori senza competenze nell'orchestrazione di contenitori.</span><span class="sxs-lookup"><span data-stu-id="632a9-122">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="632a9-123">[Ricerca di Azure](/azure/search/) viene usato per creare una soluzione di ricerca aziendale sul contenuto privato eterogeneo.</span><span class="sxs-lookup"><span data-stu-id="632a9-123">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="632a9-124">[Service Fabric](/azure/service-fabric/) è una piattaforma di sistemi distribuiti che semplifica la disposizione di pacchetti, la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili.</span><span class="sxs-lookup"><span data-stu-id="632a9-124">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
