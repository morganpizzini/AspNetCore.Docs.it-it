---
title: Immutabilità chiave e impostazioni chiave in ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione delle API di ASP.NET Core chiave di immutabilità della chiave di protezione dati.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 8efca1d96945cb7af0132b27801b23a45714e08a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061249"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a><span data-ttu-id="0886e-103">Immutabilità chiave e impostazioni chiave in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0886e-103">Key immutability and key settings in ASP.NET Core</span></span>

<span data-ttu-id="0886e-104">Quando un oggetto viene salvato in modo permanente nell'archivio di backup, la relativa rappresentazione è sempre fissa.</span><span class="sxs-lookup"><span data-stu-id="0886e-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="0886e-105">È possibile aggiungere nuovi dati all'archivio di backup, ma i dati esistenti non possono mai essere mutati.</span><span class="sxs-lookup"><span data-stu-id="0886e-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="0886e-106">Lo scopo principale di questo comportamento è impedire il danneggiamento dei dati.</span><span class="sxs-lookup"><span data-stu-id="0886e-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="0886e-107">Una conseguenza di questo comportamento è che una volta che una chiave viene scritta nell'archivio di backup, non è modificabile.</span><span class="sxs-lookup"><span data-stu-id="0886e-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="0886e-108">La creazione, l'attivazione e le date di scadenza non possono mai essere modificate, sebbene possano essere revocate tramite `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="0886e-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="0886e-109">Inoltre, le informazioni algoritmiche sottostanti, il materiale della chiave master e la crittografia delle proprietà dei dati inattivi non sono modificabili.</span><span class="sxs-lookup"><span data-stu-id="0886e-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="0886e-110">Se lo sviluppatore modifica un'impostazione che influisce sulla persistenza della chiave, queste modifiche non saranno effettive fino alla successiva generazione di una chiave, tramite una chiamata esplicita a `IKeyManager.CreateNewKey` o tramite il comportamento di [generazione automatica della chiave](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) del sistema di protezione dati.</span><span class="sxs-lookup"><span data-stu-id="0886e-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="0886e-111">Le impostazioni che influiscono sulla persistenza della chiave sono le seguenti:</span><span class="sxs-lookup"><span data-stu-id="0886e-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="0886e-112">Durata della chiave predefinita</span><span class="sxs-lookup"><span data-stu-id="0886e-112">The default key lifetime</span></span>](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [<span data-ttu-id="0886e-113">Meccanismo di crittografia dei tasti inattivi</span><span class="sxs-lookup"><span data-stu-id="0886e-113">The key encryption at rest mechanism</span></span>](xref:security/data-protection/implementation/key-encryption-at-rest)

* [<span data-ttu-id="0886e-114">Informazioni algoritmiche contenute all'interno della chiave</span><span class="sxs-lookup"><span data-stu-id="0886e-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="0886e-115">Se queste impostazioni sono necessarie per avviare prima del tempo di esecuzione della chiave automatica successiva, provare a effettuare una chiamata esplicita a `IKeyManager.CreateNewKey` per forzare la creazione di una nuova chiave.</span><span class="sxs-lookup"><span data-stu-id="0886e-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="0886e-116">Ricordarsi di specificare una data di attivazione esplicita ({Now + 2 Days} è una regola empirica adatta per consentire la propagazione della modifica) e la data di scadenza nella chiamata.</span><span class="sxs-lookup"><span data-stu-id="0886e-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="0886e-117">Tutte le applicazioni che toccano il repository devono specificare le stesse impostazioni con i `IDataProtectionBuilder` metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="0886e-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="0886e-118">In caso contrario, le proprietà della chiave permanente dipendono dalla specifica applicazione che ha richiamato le routine di generazione delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="0886e-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
