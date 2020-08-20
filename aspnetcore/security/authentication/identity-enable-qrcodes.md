---
title: Abilitare la generazione di codice a matrice per le app TOTP Authenticator in ASP.NET Core
author: rick-anderson
description: Scopri come abilitare la generazione di codice a matrice per le app TOTP Authenticator che funzionano con ASP.NET Core Autenticazione a due fattori.
ms.author: riande
ms.date: 08/14/2018
no-loc:
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
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: e61aa925262fc9fe25c7bb2d37958cfaa308aeaf
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630796"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="85139-103">Abilitare la generazione di codice a matrice per le app TOTP Authenticator in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85139-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="85139-104">Per i codici QR è necessario ASP.NET Core 2,0 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="85139-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="85139-105">ASP.NET Core fornisce supporto per le applicazioni di autenticazione per l'autenticazione singola.</span><span class="sxs-lookup"><span data-stu-id="85139-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="85139-106">Le app di autenticazione a due fattori (2FA), che usano un algoritmo di password monouso basato sul tempo (TOTP), sono l'approccio consigliato per 2FA.</span><span class="sxs-lookup"><span data-stu-id="85139-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="85139-107">2FA con TOTP è preferibile a SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="85139-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="85139-108">Un'app Authenticator fornisce un codice da 6 a 8 cifre che gli utenti devono immettere dopo aver confermato il nome utente e la password.</span><span class="sxs-lookup"><span data-stu-id="85139-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="85139-109">In genere, un'app autenticatore viene installata in uno smartphone.</span><span class="sxs-lookup"><span data-stu-id="85139-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="85139-110">I modelli di app Web ASP.NET Core supportano gli autenticatori, ma non forniscono il supporto per la generazione di QRCode.</span><span class="sxs-lookup"><span data-stu-id="85139-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="85139-111">I generatori QRCode semplificano la configurazione di 2FA.</span><span class="sxs-lookup"><span data-stu-id="85139-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="85139-112">Questo documento illustra come aggiungere la generazione di [codice](https://wikipedia.org/wiki/QR_code) a matrice alla pagina di configurazione di 2FA.</span><span class="sxs-lookup"><span data-stu-id="85139-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="85139-113">L'autenticazione a due fattori non viene eseguita con un provider di autenticazione esterno, ad esempio [Google](xref:security/authentication/google-logins) o [Facebook](xref:security/authentication/facebook-logins).</span><span class="sxs-lookup"><span data-stu-id="85139-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="85139-114">Gli account di accesso esterni sono protetti da qualsiasi meccanismo fornito dal provider di accesso esterno.</span><span class="sxs-lookup"><span data-stu-id="85139-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="85139-115">Si consideri, ad esempio, che il provider di autenticazione [Microsoft](xref:security/authentication/microsoft-logins) richiede una chiave hardware o un altro approccio 2FA.</span><span class="sxs-lookup"><span data-stu-id="85139-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="85139-116">Se i modelli predefiniti applicavano "local" 2FA, gli utenti sarebbero necessari per soddisfare due approcci 2FA, che non è uno scenario di uso comune.</span><span class="sxs-lookup"><span data-stu-id="85139-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="85139-117">Aggiunta di codici QR alla pagina di configurazione di 2FA</span><span class="sxs-lookup"><span data-stu-id="85139-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="85139-118">Queste istruzioni usano *qrcode.js* dal https://davidshimjs.github.io/qrcodejs/ repository.</span><span class="sxs-lookup"><span data-stu-id="85139-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="85139-119">Scaricare la [ libreria javascriptqrcode.js](https://davidshimjs.github.io/qrcodejs/) `wwwroot\lib` nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="85139-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="85139-120">Seguire le istruzioni riportate in [impalcatura Identity ](xref:security/authentication/scaffold-identity) per generare */areas/ Identity /pages/account/Manage/EnableAuthenticator.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="85139-120">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>
* <span data-ttu-id="85139-121">In */areas/ Identity /pages/account/Manage/EnableAuthenticator.cshtml*individuare la `Scripts` sezione alla fine del file:</span><span class="sxs-lookup"><span data-stu-id="85139-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="85139-122">In *pages/account/Manage/EnableAuthenticator. cshtml* ( Razor pages) o *views/Manage/EnableAuthenticator. cshtml* (MVC) individuare la `Scripts` sezione alla fine del file:</span><span class="sxs-lookup"><span data-stu-id="85139-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="85139-123">Aggiornare la `Scripts` sezione per aggiungere un riferimento alla `qrcodejs` libreria aggiunta e una chiamata per generare il codice a matrice.</span><span class="sxs-lookup"><span data-stu-id="85139-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="85139-124">Dovrebbe avere un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="85139-124">It should look as follows:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* <span data-ttu-id="85139-125">Eliminare il paragrafo che fornisce collegamenti a queste istruzioni.</span><span class="sxs-lookup"><span data-stu-id="85139-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="85139-126">Eseguire l'app e verificare che sia possibile eseguire la scansione del codice a matrice e convalidare il codice dimostrato dall'autenticatore.</span><span class="sxs-lookup"><span data-stu-id="85139-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="85139-127">Modificare il nome del sito nel codice a matrice</span><span class="sxs-lookup"><span data-stu-id="85139-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="85139-128">Il nome del sito nel codice a matrice viene ricavato dal nome del progetto scelto durante la creazione iniziale del progetto.</span><span class="sxs-lookup"><span data-stu-id="85139-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="85139-129">È possibile modificarlo cercando il `GenerateQrCodeUri(string email, string unformattedKey)` metodo in */areas/ Identity /pages/account/Manage/EnableAuthenticator.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="85139-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="85139-130">Il nome del sito nel codice a matrice viene ricavato dal nome del progetto scelto durante la creazione iniziale del progetto.</span><span class="sxs-lookup"><span data-stu-id="85139-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="85139-131">È possibile modificarlo cercando il `GenerateQrCodeUri(string email, string unformattedKey)` metodo nel file *pages/account/Manage/EnableAuthenticator. cshtml. cs* ( Razor pages) o nel file *Controllers/ManageController. cs* (MVC).</span><span class="sxs-lookup"><span data-stu-id="85139-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="85139-132">Il codice predefinito del modello è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="85139-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="85139-133">Il secondo parametro nella chiamata a `string.Format` è il nome del sito, tratto dal nome della soluzione.</span><span class="sxs-lookup"><span data-stu-id="85139-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="85139-134">Può essere modificato in qualsiasi valore, ma deve sempre essere codificato in URL.</span><span class="sxs-lookup"><span data-stu-id="85139-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="85139-135">Uso di una libreria di codice QR diversa</span><span class="sxs-lookup"><span data-stu-id="85139-135">Using a different QR Code library</span></span>

<span data-ttu-id="85139-136">È possibile sostituire la libreria di codici QR con la libreria preferita.</span><span class="sxs-lookup"><span data-stu-id="85139-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="85139-137">Il codice HTML contiene un `qrCode` elemento in cui è possibile inserire un codice a matrice in base a qualsiasi meccanismo fornito dalla libreria.</span><span class="sxs-lookup"><span data-stu-id="85139-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="85139-138">L'URL correttamente formattato per il codice a matrice è disponibile in:</span><span class="sxs-lookup"><span data-stu-id="85139-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="85139-139">`AuthenticatorUri` Proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="85139-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="85139-140">`data-url` Proprietà nell' `qrCodeData` elemento.</span><span class="sxs-lookup"><span data-stu-id="85139-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="85139-141">Sfasamento dell'ora del client e del server TOTP</span><span class="sxs-lookup"><span data-stu-id="85139-141">TOTP client and server time skew</span></span>

<span data-ttu-id="85139-142">L'autenticazione TOTP (Time-based password monouso) dipende sia dal dispositivo server che dall'autenticatore che ha un'ora esatta.</span><span class="sxs-lookup"><span data-stu-id="85139-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="85139-143">I token durano solo per 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="85139-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="85139-144">Se gli accessi 2FA di TOTP non riescono, verificare che l'ora del server sia accurata e preferibilmente sincronizzata con un servizio NTP accurato.</span><span class="sxs-lookup"><span data-stu-id="85139-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
