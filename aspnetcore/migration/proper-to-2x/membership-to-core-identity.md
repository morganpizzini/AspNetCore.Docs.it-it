---
title: Eseguire la migrazione da ASP.NET Membership Authentication a ASP.NET Core 2,0 Identity
author: isaac2004
description: Informazioni su come eseguire la migrazione di app ASP.NET esistenti usando l'autenticazione di appartenenza a ASP.NET Core 2,0 Identity .
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
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
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: de9d1e5f6f595269595212fbab60d12dfd5a29e4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633643"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a><span data-ttu-id="e26c1-103">Eseguire la migrazione da ASP.NET Membership Authentication a ASP.NET Core 2,0 Identity</span><span class="sxs-lookup"><span data-stu-id="e26c1-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="e26c1-104">Di [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="e26c1-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="e26c1-105">Questo articolo illustra la migrazione dello schema del database per le app ASP.NET usando l'autenticazione di appartenenza a ASP.NET Core 2,0 Identity .</span><span class="sxs-lookup"><span data-stu-id="e26c1-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="e26c1-106">Questo documento illustra i passaggi necessari per eseguire la migrazione dello schema del database per le app basate sull'appartenenza a ASP.NET allo schema del database usato per ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="e26c1-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="e26c1-107">Per altre informazioni sulla migrazione dall'autenticazione basata sull'appartenenza a ASP.NET a ASP.NET Identity , vedere [eseguire la migrazione di un'app esistente dall' Identity appartenenza a SQL a ASP.NET ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="e26c1-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="e26c1-108">Per ulteriori informazioni su ASP.NET Core Identity , vedere [Introduzione a Identity su ASP.NET Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="e26c1-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="e26c1-109">Revisione dello schema di appartenenza</span><span class="sxs-lookup"><span data-stu-id="e26c1-109">Review of Membership schema</span></span>

<span data-ttu-id="e26c1-110">Prima di ASP.NET 2,0, gli sviluppatori avevano il compito di creare l'intero processo di autenticazione e autorizzazione per le proprie app.</span><span class="sxs-lookup"><span data-stu-id="e26c1-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="e26c1-111">Con ASP.NET 2,0 è stata introdotta l'appartenenza, che fornisce una soluzione standard per la gestione della sicurezza nelle app ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e26c1-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="e26c1-112">Gli sviluppatori sono ora in grado di eseguire il bootstrap di uno schema in un database di SQL Server con il comando [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) .</span><span class="sxs-lookup"><span data-stu-id="e26c1-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="e26c1-113">Dopo aver eseguito questo comando, nel database sono state create le tabelle seguenti.</span><span class="sxs-lookup"><span data-stu-id="e26c1-113">After running this command, the following tables were created in the database.</span></span>

  ![Tabelle di appartenenza](identity/_static/membership-tables.png)

<span data-ttu-id="e26c1-115">Per eseguire la migrazione di app esistenti a ASP.NET Core 2,0 Identity , è necessario eseguire la migrazione dei dati in queste tabelle alle tabelle utilizzate dal nuovo Identity schema.</span><span class="sxs-lookup"><span data-stu-id="e26c1-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="no-locaspnet-core-identity-20-schema"></a><span data-ttu-id="e26c1-116">ASP.NET Core Identity Schema 2,0</span><span class="sxs-lookup"><span data-stu-id="e26c1-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="e26c1-117">ASP.NET Core 2,0 segue il [Identity](/aspnet/identity/index) principio introdotto in ASP.NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="e26c1-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="e26c1-118">Sebbene il principio sia condiviso, l'implementazione tra i Framework è diversa, anche tra le versioni di ASP.NET Core (vedere [eseguire la migrazione dell'autenticazione e Identity a ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="e26c1-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="e26c1-119">Il modo più rapido per visualizzare lo schema per ASP.NET Core 2,0 Identity consiste nel creare una nuova app ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="e26c1-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="e26c1-120">Seguire questa procedura in Visual Studio 2017:</span><span class="sxs-lookup"><span data-stu-id="e26c1-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="e26c1-121">Selezionare **File** > **New** (Nuovo)  > **Project** (Progetto).</span><span class="sxs-lookup"><span data-stu-id="e26c1-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="e26c1-122">Creare un nuovo progetto di **applicazione Web di ASP.NET Core** denominato *Core Identity Sample*.</span><span class="sxs-lookup"><span data-stu-id="e26c1-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="e26c1-123">Selezionare **ASP.NET Core 2,0** nell'elenco a discesa, quindi selezionare **applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="e26c1-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="e26c1-124">Questo modello genera un'app [ Razor pagine](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="e26c1-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="e26c1-125">Prima di fare clic su **OK**, fare clic su **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="e26c1-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="e26c1-126">Scegliere gli **account utente singoli** per i Identity modelli.</span><span class="sxs-lookup"><span data-stu-id="e26c1-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="e26c1-127">Infine, fare clic su **OK**, quindi su **OK**.</span><span class="sxs-lookup"><span data-stu-id="e26c1-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="e26c1-128">Visual Studio crea un progetto usando il ASP.NET Core Identity modello.</span><span class="sxs-lookup"><span data-stu-id="e26c1-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="e26c1-129">Selezionare **strumenti**gestione pacchetti  >  **NuGet**  >  **console di gestione** pacchetti per aprire la finestra **console di gestione pacchetti** (PMC).</span><span class="sxs-lookup"><span data-stu-id="e26c1-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="e26c1-130">Passare alla radice del progetto in PMC ed eseguire il comando [Entity Framework (EF) Core](/ef/core) `Update-Database` .</span><span class="sxs-lookup"><span data-stu-id="e26c1-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="e26c1-131">ASP.NET Core 2,0 Identity utilizza EF core per interagire con il database che archivia i dati di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="e26c1-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="e26c1-132">Per consentire il funzionamento dell'app appena creata, è necessario che sia presente un database per archiviare questi dati.</span><span class="sxs-lookup"><span data-stu-id="e26c1-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="e26c1-133">Dopo la creazione di una nuova app, il modo più rapido per esaminare lo schema in un ambiente di database consiste nel creare il database usando [migrazioni EF Core](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="e26c1-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="e26c1-134">Questo processo crea un database, localmente o altrove, che simula lo schema.</span><span class="sxs-lookup"><span data-stu-id="e26c1-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="e26c1-135">Per ulteriori informazioni, consultare la documentazione precedente.</span><span class="sxs-lookup"><span data-stu-id="e26c1-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="e26c1-136">EF Core comandi utilizzano la stringa di connessione per il database specificato in *appsettings.jssu*.</span><span class="sxs-lookup"><span data-stu-id="e26c1-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="e26c1-137">La stringa di connessione seguente è destinata a un database in *localhost* denominato *ASP-NET-Core-Identity*.</span><span class="sxs-lookup"><span data-stu-id="e26c1-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="e26c1-138">In questa impostazione EF Core è configurato per l'utilizzo della `DefaultConnection` stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="e26c1-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="e26c1-139">Selezionare **Visualizza**  >  **Esplora oggetti di SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="e26c1-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="e26c1-140">Espandere il nodo corrispondente al nome del database specificato nella `ConnectionStrings:DefaultConnection` proprietà di *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e26c1-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="e26c1-141">Il `Update-Database` comando ha creato il database specificato con lo schema e tutti i dati necessari per l'inizializzazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="e26c1-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="e26c1-142">Nell'immagine seguente viene illustrata la struttura della tabella creata con i passaggi precedenti.</span><span class="sxs-lookup"><span data-stu-id="e26c1-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![::: NO-LOC (Identity)::: Tables](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="e26c1-144">Migrazione dello schema</span><span class="sxs-lookup"><span data-stu-id="e26c1-144">Migrate the schema</span></span>

<span data-ttu-id="e26c1-145">Esistono differenze minime nelle strutture e nei campi della tabella sia per l'appartenenza che per ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="e26c1-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="e26c1-146">Il modello è stato modificato in modo sostanziale per l'autenticazione/autorizzazione con ASP.NET e app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e26c1-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="e26c1-147">Gli oggetti chiave ancora utilizzati con Identity sono *utenti* e *ruoli*.</span><span class="sxs-lookup"><span data-stu-id="e26c1-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="e26c1-148">Di seguito sono riportate le tabelle di mapping per *utenti*, *ruoli*e *UserRoles*.</span><span class="sxs-lookup"><span data-stu-id="e26c1-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="e26c1-149">Utenti</span><span class="sxs-lookup"><span data-stu-id="e26c1-149">Users</span></span>

|Identity<br><span data-ttu-id="e26c1-150">( `dbo.AspNetUsers` ) (colonna)</span><span class="sxs-lookup"><span data-stu-id="e26c1-150">(`dbo.AspNetUsers`) column</span></span>  |<span data-ttu-id="e26c1-151">Type</span><span class="sxs-lookup"><span data-stu-id="e26c1-151">Type</span></span>     |<span data-ttu-id="e26c1-152">Appartenenze</span><span class="sxs-lookup"><span data-stu-id="e26c1-152">Membership</span></span><br><span data-ttu-id="e26c1-153">( `dbo.aspnet_Users`  /  `dbo.aspnet_Membership` ) (colonna)</span><span class="sxs-lookup"><span data-stu-id="e26c1-153">(`dbo.aspnet_Users` / `dbo.aspnet_Membership`) column</span></span>|<span data-ttu-id="e26c1-154">Type</span><span class="sxs-lookup"><span data-stu-id="e26c1-154">Type</span></span>      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> <span data-ttu-id="e26c1-155">Non tutti i mapping dei campi assomigliano a relazioni uno-a-uno dall'appartenenza a ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="e26c1-155">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="e26c1-156">La tabella precedente accetta lo schema utente di appartenenza predefinito e ne esegue il mapping allo ASP.NET Core Identity schema.</span><span class="sxs-lookup"><span data-stu-id="e26c1-156">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="e26c1-157">È necessario eseguire il mapping manuale di tutti gli altri campi personalizzati usati per l'appartenenza.</span><span class="sxs-lookup"><span data-stu-id="e26c1-157">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="e26c1-158">In questo mapping non è disponibile alcuna mappa per le password, perché i criteri password e i Salt delle password non vengono migrati tra i due.</span><span class="sxs-lookup"><span data-stu-id="e26c1-158">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="e26c1-159">**È consigliabile lasciare la password null e richiedere agli utenti di reimpostare le password.**</span><span class="sxs-lookup"><span data-stu-id="e26c1-159">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="e26c1-160">In ASP.NET Core Identity , `LockoutEnd` deve essere impostato su una data futura se l'utente è bloccato. Questa operazione viene mostrata nello script di migrazione.</span><span class="sxs-lookup"><span data-stu-id="e26c1-160">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="e26c1-161">Ruoli</span><span class="sxs-lookup"><span data-stu-id="e26c1-161">Roles</span></span>

|Identity<br><span data-ttu-id="e26c1-162">( `dbo.AspNetRoles` ) (colonna)</span><span class="sxs-lookup"><span data-stu-id="e26c1-162">(`dbo.AspNetRoles`) column</span></span>|<span data-ttu-id="e26c1-163">Type</span><span class="sxs-lookup"><span data-stu-id="e26c1-163">Type</span></span>|<span data-ttu-id="e26c1-164">Appartenenze</span><span class="sxs-lookup"><span data-stu-id="e26c1-164">Membership</span></span><br><span data-ttu-id="e26c1-165">( `dbo.aspnet_Roles` ) (colonna)</span><span class="sxs-lookup"><span data-stu-id="e26c1-165">(`dbo.aspnet_Roles`) column</span></span>|<span data-ttu-id="e26c1-166">Type</span><span class="sxs-lookup"><span data-stu-id="e26c1-166">Type</span></span>|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a><span data-ttu-id="e26c1-167">Ruoli utente</span><span class="sxs-lookup"><span data-stu-id="e26c1-167">User Roles</span></span>

|Identity<br><span data-ttu-id="e26c1-168">( `dbo.AspNetUserRoles` ) (colonna)</span><span class="sxs-lookup"><span data-stu-id="e26c1-168">(`dbo.AspNetUserRoles`) column</span></span>|<span data-ttu-id="e26c1-169">Type</span><span class="sxs-lookup"><span data-stu-id="e26c1-169">Type</span></span>|<span data-ttu-id="e26c1-170">Appartenenze</span><span class="sxs-lookup"><span data-stu-id="e26c1-170">Membership</span></span><br><span data-ttu-id="e26c1-171">( `dbo.aspnet_UsersInRoles` ) (colonna)</span><span class="sxs-lookup"><span data-stu-id="e26c1-171">(`dbo.aspnet_UsersInRoles`) column</span></span>|<span data-ttu-id="e26c1-172">Type</span><span class="sxs-lookup"><span data-stu-id="e26c1-172">Type</span></span>|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

<span data-ttu-id="e26c1-173">Quando si crea uno script di migrazione per *utenti* e *ruoli*, fare riferimento alle tabelle di mapping precedenti.</span><span class="sxs-lookup"><span data-stu-id="e26c1-173">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="e26c1-174">Nell'esempio seguente si presuppone che si disponga di due database in un server di database.</span><span class="sxs-lookup"><span data-stu-id="e26c1-174">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="e26c1-175">Un database contiene i dati e lo schema di appartenenza ASP.NET esistente.</span><span class="sxs-lookup"><span data-stu-id="e26c1-175">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="e26c1-176">L'altro database di \* Identity esempio principale\* è stato creato usando i passaggi descritti in precedenza.</span><span class="sxs-lookup"><span data-stu-id="e26c1-176">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="e26c1-177">Per ulteriori informazioni, sono inclusi i commenti inline.</span><span class="sxs-lookup"><span data-stu-id="e26c1-177">Comments are included inline for more details.</span></span>

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

<span data-ttu-id="e26c1-178">Al termine dello script precedente, l' ASP.NET Core Identity app creata in precedenza viene popolata con gli utenti di appartenenza.</span><span class="sxs-lookup"><span data-stu-id="e26c1-178">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="e26c1-179">Gli utenti devono modificare le password prima di eseguire l'accesso.</span><span class="sxs-lookup"><span data-stu-id="e26c1-179">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="e26c1-180">Se il sistema di appartenenze avesse utenti con nomi utente che non corrispondono all'indirizzo di posta elettronica, le modifiche sono necessarie per l'app creata in precedenza per soddisfare questo problema.</span><span class="sxs-lookup"><span data-stu-id="e26c1-180">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="e26c1-181">Il modello predefinito prevede che `UserName` e `Email` siano uguali.</span><span class="sxs-lookup"><span data-stu-id="e26c1-181">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="e26c1-182">Per le situazioni in cui sono diverse, è necessario modificare il processo di accesso per usare `UserName` anziché `Email` .</span><span class="sxs-lookup"><span data-stu-id="e26c1-182">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="e26c1-183">Nella `PageModel` pagina di accesso di, che si trova in *Pages\Account\Login.cshtml.cs*, rimuovere l' `[EmailAddress]` attributo dalla proprietà *email* .</span><span class="sxs-lookup"><span data-stu-id="e26c1-183">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="e26c1-184">Rinominare il *nome utente*.</span><span class="sxs-lookup"><span data-stu-id="e26c1-184">Rename it to *UserName*.</span></span> <span data-ttu-id="e26c1-185">Questa operazione richiede una modifica ovunque `EmailAddress` sia indicato, nella *vista* e in *PageModel*.</span><span class="sxs-lookup"><span data-stu-id="e26c1-185">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="e26c1-186">Il risultato è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="e26c1-186">The result looks like the following:</span></span>

 ![Accesso fisso](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="e26c1-188">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="e26c1-188">Next steps</span></span>

<span data-ttu-id="e26c1-189">In questa esercitazione si è appreso come trasferire gli utenti dall'appartenenza SQL a ASP.NET Core 2,0 Identity .</span><span class="sxs-lookup"><span data-stu-id="e26c1-189">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="e26c1-190">Per ulteriori informazioni su ASP.NET Core Identity , vedere [Introduzione a Identity ](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="e26c1-190">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
