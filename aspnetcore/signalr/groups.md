---
title: Gestire utenti e gruppi inSignalR
author: bradygaster
description: Panoramica di ASP.NET Core SignalR gestione di utenti e gruppi.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: af498575899fcfa407aba9f9f49c0bfeabadc7a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776298"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="201be-103">Gestire utenti e gruppi inSignalR</span><span class="sxs-lookup"><span data-stu-id="201be-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="201be-104">Di [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="201be-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="201be-105">consente l'invio di messaggi a tutte le connessioni associate a un utente specifico, oltre che ai gruppi denominati di connessioni.</span><span class="sxs-lookup"><span data-stu-id="201be-105"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="201be-106">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(procedura per il download)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="201be-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="201be-107">Utenti inSignalR</span><span class="sxs-lookup"><span data-stu-id="201be-107">Users in SignalR</span></span>

SignalR<span data-ttu-id="201be-108">consente di inviare messaggi a tutte le connessioni associate a un utente specifico.</span><span class="sxs-lookup"><span data-stu-id="201be-108"> allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="201be-109">Per impostazione predefinita SignalR , utilizza `ClaimTypes.NameIdentifier` l'oggetto `ClaimsPrincipal` dall'oggetto associato alla connessione come identificatore utente.</span><span class="sxs-lookup"><span data-stu-id="201be-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="201be-110">Un singolo utente può avere più connessioni a un' SignalR app.</span><span class="sxs-lookup"><span data-stu-id="201be-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="201be-111">Ad esempio, un utente potrebbe essere connesso sul desktop e sul telefono.</span><span class="sxs-lookup"><span data-stu-id="201be-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="201be-112">Ogni dispositivo ha una connessione SignalR separata, ma tutti sono associati allo stesso utente.</span><span class="sxs-lookup"><span data-stu-id="201be-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="201be-113">Se un messaggio viene inviato all'utente, tutte le connessioni associate a tale utente riceveranno il messaggio.</span><span class="sxs-lookup"><span data-stu-id="201be-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="201be-114">È possibile accedere all'identificatore utente per una connessione tramite la `Context.UserIdentifier` proprietà nell'hub.</span><span class="sxs-lookup"><span data-stu-id="201be-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="201be-115">Inviare un messaggio a un utente specifico passando l'identificatore utente alla `User` funzione nel metodo dell'hub, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="201be-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="201be-116">L'identificatore utente distingue tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="201be-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="201be-117">Gruppi inSignalR</span><span class="sxs-lookup"><span data-stu-id="201be-117">Groups in SignalR</span></span>

<span data-ttu-id="201be-118">Un gruppo è una raccolta di connessioni associate a un nome.</span><span class="sxs-lookup"><span data-stu-id="201be-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="201be-119">I messaggi possono essere inviati a tutte le connessioni in un gruppo.</span><span class="sxs-lookup"><span data-stu-id="201be-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="201be-120">I gruppi sono il metodo consigliato per l'invio a una connessione o a più connessioni perché i gruppi sono gestiti dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="201be-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="201be-121">Una connessione può essere un membro di più gruppi.</span><span class="sxs-lookup"><span data-stu-id="201be-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="201be-122">In questo modo i gruppi sono ideali per un elemento come un'applicazione di chat, in cui ogni stanza può essere rappresentata come un gruppo.</span><span class="sxs-lookup"><span data-stu-id="201be-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="201be-123">Le connessioni possono essere aggiunte o rimosse dai gruppi tramite `AddToGroupAsync` i `RemoveFromGroupAsync` metodi e.</span><span class="sxs-lookup"><span data-stu-id="201be-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="201be-124">L'appartenenza al gruppo non viene mantenuta quando si riconnette una connessione.</span><span class="sxs-lookup"><span data-stu-id="201be-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="201be-125">La connessione deve essere nuovamente aggiunta al gruppo quando viene ristabilita.</span><span class="sxs-lookup"><span data-stu-id="201be-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="201be-126">Non è possibile contare i membri di un gruppo, poiché queste informazioni non sono disponibili se l'applicazione viene ridimensionata in più server.</span><span class="sxs-lookup"><span data-stu-id="201be-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="201be-127">Per proteggere l'accesso alle risorse durante l'uso di gruppi, usare la funzionalità di [autenticazione e autorizzazione](xref:signalr/authn-and-authz) in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="201be-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="201be-128">Se si aggiungono utenti a un gruppo solo quando le credenziali sono valide per il gruppo, i messaggi inviati a tale gruppo passeranno solo agli utenti autorizzati.</span><span class="sxs-lookup"><span data-stu-id="201be-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="201be-129">Tuttavia, i gruppi non sono una funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="201be-129">However, groups are not a security feature.</span></span> <span data-ttu-id="201be-130">Le attestazioni di autenticazione hanno funzionalità che non vengono eseguite dai gruppi, ad esempio la scadenza e la revoca.</span><span class="sxs-lookup"><span data-stu-id="201be-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="201be-131">Se l'autorizzazione dell'utente per accedere al gruppo viene revocata, è necessario rilevarla manualmente e rimuoverle dal gruppo.</span><span class="sxs-lookup"><span data-stu-id="201be-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="201be-132">I nomi dei gruppi fanno distinzione tra maiuscole e minuscole</span><span class="sxs-lookup"><span data-stu-id="201be-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="201be-133">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="201be-133">Related resources</span></span>

* [<span data-ttu-id="201be-134">Operazioni preliminari</span><span class="sxs-lookup"><span data-stu-id="201be-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="201be-135">Hub</span><span class="sxs-lookup"><span data-stu-id="201be-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="201be-136">Pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="201be-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
