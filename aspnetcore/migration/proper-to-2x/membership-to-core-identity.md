---
title: Eseguire la migrazione da ASP.NET Membership Authentication a ASP.NET Core 2,0 Identity
author: isaac2004
description: Informazioni su come eseguire la migrazione di app ASP.NET esistenti usando l'autenticazione di appartenenza a ASP.NET Core 2,0 Identity .
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
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
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: d981c424fd2d6cad95b9164420f093672325c347
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051356"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a>Eseguire la migrazione da ASP.NET Membership Authentication a ASP.NET Core 2,0 Identity

Di [Isaac Levin](https://isaaclevin.com)

Questo articolo illustra la migrazione dello schema del database per le app ASP.NET usando l'autenticazione di appartenenza a ASP.NET Core 2,0 Identity .

> [!NOTE]
> Questo documento illustra i passaggi necessari per eseguire la migrazione dello schema del database per le app basate sull'appartenenza a ASP.NET allo schema del database usato per ASP.NET Core Identity . Per altre informazioni sulla migrazione dall'autenticazione basata sull'appartenenza a ASP.NET a ASP.NET Identity , vedere [eseguire la migrazione di un'app esistente dall' Identity appartenenza a SQL a ASP.NET ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). Per ulteriori informazioni su ASP.NET Core Identity , vedere [Introduzione a Identity su ASP.NET Core](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Revisione dello schema di appartenenza

Prima di ASP.NET 2,0, gli sviluppatori avevano il compito di creare l'intero processo di autenticazione e autorizzazione per le proprie app. Con ASP.NET 2,0 è stata introdotta l'appartenenza, che fornisce una soluzione standard per la gestione della sicurezza nelle app ASP.NET. Gli sviluppatori sono ora in grado di eseguire il bootstrap di uno schema in un database di SQL Server con il <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> comando. Dopo aver eseguito questo comando, nel database sono state create le tabelle seguenti.

  ![Tabelle di appartenenza](identity/_static/membership-tables.png)

Per eseguire la migrazione di app esistenti a ASP.NET Core 2,0 Identity , è necessario eseguire la migrazione dei dati in queste tabelle alle tabelle utilizzate dal nuovo Identity schema.

## <a name="no-locaspnet-core-identity-20-schema"></a>ASP.NET Core Identity Schema 2,0

ASP.NET Core 2,0 segue il [Identity](/aspnet/identity/index) principio introdotto in ASP.NET 4,5. Sebbene il principio sia condiviso, l'implementazione tra i Framework è diversa, anche tra le versioni di ASP.NET Core (vedere [eseguire la migrazione dell'autenticazione e Identity a ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).

Il modo più rapido per visualizzare lo schema per ASP.NET Core 2,0 Identity consiste nel creare una nuova app ASP.NET Core 2,0. Seguire questa procedura in Visual Studio 2017:

1. Selezionare **File** > **New** (Nuovo)  > **Project** (Progetto).
1. Creare un nuovo progetto di **applicazione Web di ASP.NET Core** denominato *Core Identity Sample* .
1. Selezionare **ASP.NET Core 2,0** nell'elenco a discesa, quindi selezionare **applicazione Web** . Questo modello genera un'app [ Razor pagine](xref:razor-pages/index) . Prima di fare clic su **OK** , fare clic su **Modifica autenticazione** .
1. Scegliere gli **account utente singoli** per i Identity modelli. Infine, fare clic su **OK** , quindi su **OK** . Visual Studio crea un progetto usando il ASP.NET Core Identity modello.
1. Selezionare **strumenti** gestione pacchetti  >  **NuGet**  >  **console di gestione** pacchetti per aprire la finestra **console di gestione pacchetti** (PMC).
1. Passare alla radice del progetto in PMC ed eseguire il comando [Entity Framework (EF) Core](/ef/core) `Update-Database` .

    ASP.NET Core 2,0 Identity utilizza EF core per interagire con il database che archivia i dati di autenticazione. Per consentire il funzionamento dell'app appena creata, è necessario che sia presente un database per archiviare questi dati. Dopo la creazione di una nuova app, il modo più rapido per esaminare lo schema in un ambiente di database consiste nel creare il database usando [migrazioni EF Core](/ef/core/managing-schemas/migrations/). Questo processo crea un database, localmente o altrove, che simula lo schema. Per ulteriori informazioni, consultare la documentazione precedente.

    EF Core comandi utilizzano la stringa di connessione per il database specificato in *appsettings.json* . La stringa di connessione seguente è destinata a un database in *localhost* denominato *ASP-NET-Core-Identity* . In questa impostazione EF Core è configurato per l'utilizzo della `DefaultConnection` stringa di connessione.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. Selezionare **Visualizza**  >  **Esplora oggetti di SQL Server** . Espandere il nodo corrispondente al nome del database specificato nella `ConnectionStrings:DefaultConnection` proprietà di *appsettings.json* .

    Il `Update-Database` comando ha creato il database specificato con lo schema e tutti i dati necessari per l'inizializzazione dell'app. Nell'immagine seguente viene illustrata la struttura della tabella creata con i passaggi precedenti.

    ![::: NO-LOC (Identity)::: Tables](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Migrazione dello schema

Esistono differenze minime nelle strutture e nei campi della tabella sia per l'appartenenza che per ASP.NET Core Identity . Il modello è stato modificato in modo sostanziale per l'autenticazione/autorizzazione con ASP.NET e app ASP.NET Core. Gli oggetti chiave ancora utilizzati con Identity sono *utenti* e *ruoli* . Di seguito sono riportate le tabelle di mapping per *utenti* , *ruoli* e *UserRoles* .

### <a name="users"></a>Utenti

|Identity<br>( `dbo.AspNetUsers` ) (colonna)  |Tipo     |Appartenenze<br>( `dbo.aspnet_Users`  /  `dbo.aspnet_Membership` ) (colonna)|Tipo      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> Non tutti i mapping dei campi assomigliano a relazioni uno-a-uno dall'appartenenza a ASP.NET Core Identity . La tabella precedente accetta lo schema utente di appartenenza predefinito e ne esegue il mapping allo ASP.NET Core Identity schema. È necessario eseguire il mapping manuale di tutti gli altri campi personalizzati usati per l'appartenenza. In questo mapping non è disponibile alcuna mappa per le password, perché i criteri password e i Salt delle password non vengono migrati tra i due. **È consigliabile lasciare la password null e richiedere agli utenti di reimpostare le password.** In ASP.NET Core Identity , `LockoutEnd` deve essere impostato su una data futura se l'utente è bloccato. Questa operazione viene mostrata nello script di migrazione.

### <a name="roles"></a>Ruoli

|Identity<br>( `dbo.AspNetRoles` ) (colonna)|Tipo|Appartenenze<br>( `dbo.aspnet_Roles` ) (colonna)|Tipo|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a>Ruoli utente

|Identity<br>( `dbo.AspNetUserRoles` ) (colonna)|Tipo|Appartenenze<br>( `dbo.aspnet_UsersInRoles` ) (colonna)|Tipo|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

Quando si crea uno script di migrazione per *utenti* e *ruoli* , fare riferimento alle tabelle di mapping precedenti. Nell'esempio seguente si presuppone che si disponga di due database in un server di database. Un database contiene i dati e lo schema di appartenenza ASP.NET esistente. L'altro database di *Identity esempio principale* è stato creato usando i passaggi descritti in precedenza. Per ulteriori informazioni, sono inclusi i commenti inline.

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

Al termine dello script precedente, l' ASP.NET Core Identity app creata in precedenza viene popolata con gli utenti di appartenenza. Gli utenti devono modificare le password prima di eseguire l'accesso.

> [!NOTE]
> Se il sistema di appartenenze avesse utenti con nomi utente che non corrispondono all'indirizzo di posta elettronica, le modifiche sono necessarie per l'app creata in precedenza per soddisfare questo problema. Il modello predefinito prevede che `UserName` e `Email` siano uguali. Per le situazioni in cui sono diverse, è necessario modificare il processo di accesso per usare `UserName` anziché `Email` .

Nella `PageModel` pagina di accesso di, che si trova in *Pages\Account\Login.cshtml.cs* , rimuovere l' `[EmailAddress]` attributo dalla proprietà *email* . Rinominare il *nome utente* . Questa operazione richiede una modifica ovunque `EmailAddress` sia indicato, nella *vista* e in *PageModel* . Il risultato è simile al seguente:

 ![Accesso fisso](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come trasferire gli utenti dall'appartenenza SQL a ASP.NET Core 2,0 Identity . Per ulteriori informazioni su ASP.NET Core Identity , vedere [Introduzione a Identity ](xref:security/authentication/identity).
