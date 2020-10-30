---
title: Limitare la durata dei payload protetti in ASP.NET Core
author: rick-anderson
description: Informazioni su come limitare la durata di un payload protetto usando le API di protezione dei dati ASP.NET Core.
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
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 74417d076399066a49271c27ff128d9de6c10f94
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060794"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="a0ac5-103">Limitare la durata dei payload protetti in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0ac5-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="a0ac5-104">Esistono scenari in cui lo sviluppatore di applicazioni desidera creare un payload protetto che scada dopo un determinato periodo di tempo.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="a0ac5-105">Ad esempio, il payload protetto potrebbe rappresentare un token di reimpostazione della password che dovrebbe essere valido solo per un'ora.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="a0ac5-106">È certamente possibile che lo sviluppatore crei un formato di payload che contenga una data di scadenza incorporata e che gli sviluppatori avanzati vogliano comunque eseguire questa operazione, ma per la maggior parte degli sviluppatori che gestiscono queste scadenze può diventare noioso.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="a0ac5-107">Per semplificare questa operazione per i destinatari degli sviluppatori, il pacchetto [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contiene API di utilità per la creazione di payload che scadono automaticamente dopo un determinato periodo di tempo.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="a0ac5-108">Queste API si bloccano dal `ITimeLimitedDataProtector` tipo.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="a0ac5-109">Utilizzo API</span><span class="sxs-lookup"><span data-stu-id="a0ac5-109">API usage</span></span>

<span data-ttu-id="a0ac5-110">L' `ITimeLimitedDataProtector` interfaccia è l'interfaccia di base per la protezione e la rimozione della protezione dei payload a tempo limitato o a scadenza automatica.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="a0ac5-111">Per creare un'istanza di un `ITimeLimitedDataProtector` , è necessario innanzitutto un'istanza di un [IDataProtector](xref:security/data-protection/consumer-apis/overview) normale costruito con uno scopo specifico.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="a0ac5-112">Quando l' `IDataProtector` istanza è disponibile, chiamare il `IDataProtector.ToTimeLimitedDataProtector` metodo di estensione per ottenere una protezione con le funzionalità di scadenza predefinite.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="a0ac5-113">`ITimeLimitedDataProtector` espone i seguenti metodi di estensione e superficie API:</span><span class="sxs-lookup"><span data-stu-id="a0ac5-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="a0ac5-114">CreateProtector (scopo della stringa): ITimeLimitedDataProtector-questa API è simile a quella esistente perché `IDataProtectionProvider.CreateProtector` può essere usata per creare [catene di scopi](xref:security/data-protection/consumer-apis/purpose-strings) da un programma di protezione con limitazioni temporali radice.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="a0ac5-115">Protect (byte [] testo normale, scadenza DateTimeOffset): byte []</span><span class="sxs-lookup"><span data-stu-id="a0ac5-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="a0ac5-116">Protect (byte [] testo non crittografato, durata TimeSpan): byte []</span><span class="sxs-lookup"><span data-stu-id="a0ac5-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="a0ac5-117">Protect (byte [] testo normale): byte []</span><span class="sxs-lookup"><span data-stu-id="a0ac5-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="a0ac5-118">Protect (testo non crittografato, scadenza DateTimeOffset): stringa</span><span class="sxs-lookup"><span data-stu-id="a0ac5-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="a0ac5-119">Protect (testo non crittografato, durata TimeSpan): stringa</span><span class="sxs-lookup"><span data-stu-id="a0ac5-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="a0ac5-120">Protect (testo non crittografato): stringa</span><span class="sxs-lookup"><span data-stu-id="a0ac5-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="a0ac5-121">Oltre ai `Protect` metodi principali che accettano solo il testo non crittografato, sono disponibili nuovi overload che consentono di specificare la data di scadenza del payload.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="a0ac5-122">La data di scadenza può essere specificata come una data assoluta (tramite un `DateTimeOffset` ) o come ora relativa (dall'ora di sistema corrente, tramite `TimeSpan` ).</span><span class="sxs-lookup"><span data-stu-id="a0ac5-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="a0ac5-123">Se viene chiamato un overload che non accetta una scadenza, il payload viene considerato mai scaduto.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="a0ac5-124">Unprotect (byte [] protectedData, out DateTimeOffset scadenza): byte []</span><span class="sxs-lookup"><span data-stu-id="a0ac5-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="a0ac5-125">Unprotect (byte [] protectedData): byte []</span><span class="sxs-lookup"><span data-stu-id="a0ac5-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="a0ac5-126">Unprotect (String protectedData, out DateTimeOffset scadenza): stringa</span><span class="sxs-lookup"><span data-stu-id="a0ac5-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="a0ac5-127">Unprotect (String protectedData): stringa</span><span class="sxs-lookup"><span data-stu-id="a0ac5-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="a0ac5-128">I `Unprotect` metodi restituiscono i dati originali non protetti.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="a0ac5-129">Se il payload non è ancora scaduto, la scadenza assoluta viene restituita come parametro out facoltativo insieme ai dati non protetti originali.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="a0ac5-130">Se il payload è scaduto, tutti gli overload del metodo Unprotect genereranno CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="a0ac5-131">Non è consigliabile usare queste API per proteggere i payload che richiedono persistenza a lungo termine o indefinito.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="a0ac5-132">"È possibile garantire che i payload protetti siano irreversibili in modo permanente dopo un mese?"</span><span class="sxs-lookup"><span data-stu-id="a0ac5-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="a0ac5-133">può fungere da regola generale. Se la risposta è No, gli sviluppatori devono prendere in considerazione API alternative.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="a0ac5-134">Nell'esempio seguente vengono usati i [percorsi di codice non di](xref:security/data-protection/configuration/non-di-scenarios) per la creazione di un'istanza del sistema di protezione dei dati.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="a0ac5-135">Per eseguire questo esempio, verificare di aver prima aggiunto un riferimento al pacchetto Microsoft. AspNetCore. dataprotection. Extensions.</span><span class="sxs-lookup"><span data-stu-id="a0ac5-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
