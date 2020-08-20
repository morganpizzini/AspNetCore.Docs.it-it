---
title: Gestione e durata delle chiavi di protezione dati in ASP.NET Core
author: rick-anderson
description: Informazioni sulla gestione e la durata delle chiavi di protezione dei dati in ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: b4578737a0ea36463b3c44254aad85a484c46090
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634475"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>Gestione e durata delle chiavi di protezione dati in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>Gestione delle chiavi

L'app tenta di rilevare il proprio ambiente operativo e gestire la configurazione della chiave autonomamente.

1. Se l'app è ospitata in [app di Azure](https://azure.microsoft.com/services/app-service/), le chiavi vengono salvate in modo permanente nella cartella *%Home%\ASP.NET\DataProtection-Keys* . La cartella è associata all'archiviazione di rete e sincronizzata in tutti i computer che ospitano l'app.
   * Le chiavi non vengono protette quando sono inattive.
   * La cartella *DataProtection-Keys* fornisce l'anello chiave a tutte le istanze di un'app in un unico slot di distribuzione.
   * Gli slot di distribuzione separati, ad esempio gli slot di gestione temporanea e di produzione, non condividono un KeyRing. Quando si esegue lo scambio tra gli slot di distribuzione, ad esempio lo scambio di staging in produzione o l'uso di un test A/B, qualsiasi app che usa la protezione dei dati non sarà in grado di decrittografare i dati archiviati usando l'anello chiave all'interno dello slot precedente. In questo modo gli utenti vengono disconnessi da un'app che usa l'autenticazione ASP.NET Core standard cookie , perché usa la protezione dei dati per proteggere i relativi cookie . Se si vuole che gli anelli chiave indipendenti dallo slot, usare un provider di anello chiave esterno, ad esempio archiviazione BLOB di Azure, Azure Key Vault, un archivio SQL o cache Redis.

1. Se il profilo utente è disponibile, le chiavi vengono salvate in modo permanente nella cartella *%LocalAppData%\ASP.NET\DataProtection-Keys* . Se il sistema operativo è Windows, le chiavi vengono crittografate a riposo tramite DPAPI.

   Deve essere abilitato anche l'[attributo setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) del pool di app. Il valore predefinito di `setProfileEnvironment` è `true`. In alcuni scenari (ad esempio, per il sistema operativo Windows), `setProfileEnvironment` è impostato su `false`. Se le chiavi non vengono archiviate nella directory del profilo utente come previsto:

   1. Passare alla cartella *%windir%/system32/inetsrv/config*.
   1. Aprire il file *applicationHost.config*.
   1. Individuare l'elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
   1. Verificare che l'attributo `setProfileEnvironment` non sia presente, condizione che corrisponde all'impostazione predefinita `true`, o impostare in modo esplicito il valore dell'attributo su `true`.

1. Se l'app è ospitata in IIS, le chiavi vengono salvate in modo permanente nel registro di sistema HKLM in una chiave del registro di sistema speciale ACL solo per l'account del processo di lavoro. Le chiavi vengono crittografate a riposo tramite la DPAPI.

1. Se nessuna di queste condizioni corrisponde, le chiavi non vengono rese permanente all'esterno del processo corrente. Quando il processo viene arrestato, vengono perse tutte le chiavi generate.

Lo sviluppatore è sempre in controllo completo ed è in grado di ignorare come e dove vengono archiviate le chiavi. Le prime tre opzioni precedenti dovrebbero fornire impostazioni predefinite valide per la maggior parte delle app, in modo analogo al modo in cui le **\<machineKey>** routine di generazione automatica ASP.NET hanno funzionato in passato. L'opzione finale fallback è l'unico scenario in cui è necessario che lo sviluppatore specifichi la [configurazione](xref:security/data-protection/configuration/overview) iniziale se desidera la persistenza della chiave, ma questo fallback si verifica solo in rari casi.

Quando si esegue l'hosting in un contenitore Docker, le chiavi devono essere rese permanente in una cartella che è un volume Docker (un volume condiviso o un volume montato dall'host che è permanente oltre la durata del contenitore) o in un provider esterno, ad esempio [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) o [Redis](https://redis.io/). Un provider esterno è utile anche negli scenari Web farm se le app non possono accedere a un volume di rete condiviso. per ulteriori informazioni, vedere [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) .

> [!WARNING]
> Se lo sviluppatore sostituisce le regole descritte in precedenza e fa riferimento al sistema di protezione dei dati in un repository di chiavi specifico, la crittografia automatica delle chiavi inattive è disabilitata. La protezione dati inattiva può essere riabilitata tramite la [configurazione](xref:security/data-protection/configuration/overview).

## <a name="key-lifetime"></a>Durata della chiave

Per impostazione predefinita, le chiavi hanno una durata di 90 giorni. Quando una chiave scade, l'app genera automaticamente una nuova chiave e imposta la nuova chiave come chiave attiva. Fino a quando le chiavi ritirate rimangono nel sistema, l'app può decrittografare i dati protetti con essi. Per ulteriori informazioni, vedere [gestione delle chiavi](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) .

## <a name="default-algorithms"></a>Algoritmi predefiniti

L'algoritmo di protezione del payload predefinito usato è AES-256-CBC per la riservatezza e HMACSHA256 per l'autenticità. Una chiave master a 512 bit, modificata ogni 90 giorni, viene usata per derivare le due sottochiavi usate per questi algoritmi in base al payload. Per ulteriori informazioni, vedere [derivazione della sottochiave](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) .

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
